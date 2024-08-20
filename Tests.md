---
title: Tests
date created: 2024-08-10T17:58:29+10:00
date modified: 2024-08-13T14:54:24+10:00
---

# Tests

- [ ] Tests
	- [ ] `api`
		- [ ] `feedback_group_api_test.rb`
		- [ ] `feedback_comment_template_api_test.rb`
		- [ ] remove unused tests
	- [ ] `factories`
		- [x] `feedback_group_factory.rb`
		- [x] `feedback_comment_template_factory.rb`
		- [x] remove unused tests
	- [ ] `models`
		- [x] `feedback_group_model_test.rb
		- [x] `feedback_comment_template_model_test.rb
		- [x] remove unused tests

```rb
rails test test/models/feedback/feedback_group_test.rb
```

## TODO

- [ ] FeedbackGroupTest#test_invalid_feedback_group_creation: ActiveRecord::StatementInvalid: Mysql2::Error: Table 'doubtfire-dev.criterion_options' doesn't exist test/models/feedback/feedback_group_test.rb:8:in `block in <class:FeedbackGroupTest>'

> [!note] Migration Files
> - `db/migrate/20240806051447_create_feedback_comment_templates.rb`


## Reset because I am scared and will get stuck

```bash
rails db:environment:set RAILS_ENV=test
RAILS_ENV=test bundle exec rake db:populate
```

```bash
RAILS_ENV=test rails db:schema:load
```