+++
title= "Handy scopes for associations"
date= 2013-09-29
tags= ["rails", "arel", "archive"]
+++

If your Rails project has a has-many association and you want a way of finding records that have no associated records, or,
records that have at least one associated record, you might find this post useful.

Suppose you have classes `Person` and `Friend` that are associated via a `has_many` relationship:

```ruby
class Person < ActiveRecord::Base
  has_many :friends
end

class Friend < ActiveRecord::Base
  belongs_to :person
end
```

How do you find `persons` that have no `friends`?

```ruby
    Person.includes(:friends).where("friends.person_id IS NULL")
```

Or that have at least one `friend`
```ruby
    Person.includes(:friends).where("friends.person_id IS NOT NULL")
```

#### Using Arel

To accomplish this with Arel
set up the following scopes on `Friend` (thanks to [@macfanatic](http://twitter.com/macfanatic) for the tip),

```ruby
class Friend
  belongs_to :person

  scope :to_somebody, ->{ where arel_table[:person_id].not_eq(nil) }
  scope :to_nobody,   ->{ where arel_table[:person_id].eq(nil) }
end
```

And then, Persons who have at least one friend:

```ruby
    Person.includes(:friends).merge(Friend.to_somebody)
```

The friendless:
```ruby
    Person.includes(:friends).merge(Friend.to_nobody)
```

*Note:* If you are on Rails 4 don't forget to append `references(:friends)` to these.
