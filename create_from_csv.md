---
title: create_from_csv
date created: 2024-08-20T12:46:37+10:00
date modified: 2024-08-20T13:08:36+10:00
---

# create_from_csv

| Field              | Type         | Null | Key | Default | Extra          |
|--------------------|--------------|------|-----|---------|----------------|
| id                 | bigint(20)   | NO   | PRI | NULL    | auto_increment |
| title              | varchar(255) | NO   |     | NULL    |                |
| order              | int(11)      | NO   |     | NULL    |                |
| task_definition_id | bigint(20)   | NO   | MUL | NULL    |                |

```rb
def import_outcomes_from_csv(file)
    result = {
      success: [],
      errors: [],
      ignored: []
    }

    data = read_file_to_str(file)

    CSV.parse(data,
              headers: true,
              header_converters: [->(i) { i.nil? ? '' : i }, :downcase, ->(hdr) { hdr.strip unless hdr.nil? }],
              converters: [->(body) { body.encode!('UTF-8', 'binary', invalid: :replace, undef: :replace, replace: '') unless body.nil? }]).each do |row|
      # Make sure we're not looking at the header or an empty line
      next if row[0] =~ /unit_code/

      begin
        LearningOutcome.create_from_csv(self, row, result)
      rescue Exception => e
        result[:errors] << { row: row, message: e.message.to_s }
      *end*
    end

    result
  end
```

```rb
def self.create_from_csv(unit, row, result)
    unit_code = row['unit_code']

    if unit_code != unit.code
      result[:ignored] << { row: row, message: "Invalid unit code. #{unit_code} does not match #{unit.code}" }
      return
    end

    ilo_number = row['ilo_number'].to_i

    abbr = row['abbreviation']
    if abbr.nil?
      result[:errors] << { row: row, message: 'Missing abbreviation' }
      return
    end

    name = row['name']
    if name.nil?
      result[:errors] << { row: row, message: 'Missing name' }
      return
    end

    description = row['description']
    if description.nil?
      result[:errors] << { row: row, message: 'Missing description' }
      return
    end

    outcome = LearningOutcome.find_or_create_by(unit_id: unit.id, abbreviation: abbr) do |outcome|
      outcome.name = name
      outcome.description = description
      outcome.ilo_number = ilo_number
    end

    outcome.save!

    result[:success] << if outcome.new_record?
                          { row: row, message: "Outcome #{abbr} created for unit" }
                        else
                          { row: row, message: "Outcome #{abbr} updated for unit" }
                        end
  end
end
```
