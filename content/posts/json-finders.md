+++
title="Postgres JSON Functions From Rails"
date=2014-05-25
tags=["rails", "postgres", "archive"]
+++

Say you are storing JSON in Postgres and need scopes to get records
based on their JSON attributes. This post covers the use of JSON functions
available in Postgres v9.3 to achieve this.

Suppose you have an Order class, containing a JSON blob that describes
the product that the order relates to:

```ruby
# == Schema Information
#
# Table name: orders
#
#  id                                    :integer  not null, primary key
#  customer_id                           :integer
#  product                               :json
#  created_at                            :datetime
#  updated_at                            :datetime

class Order < ActiveRecord::Base

end
```

Suppose `product` has the following structure:

```json
{
  "name": "Widget 01",
  "code": "W001",
  "plan": {
    "name": "Silver",
     "terms": "12 months"
   },
  "created_at": "2013-12-22 09:27:45 UTC",
  "updated_at": "2014-10-21 10:30:40 UTC"
}
```

And you want a scope on `Order` to find all orders with a particular
code - something like `Order.with_code("W001")`

If you are using version 9.3 of Postgres, you can get it to do the heavy
lifting for you using its [JSON functions](http://www.postgresql.org/docs/9.3/static/functions-json.html)
like so:

```ruby
scope :with_code, ->(code) { where("product ->> 'code' = ?", code) }
```

Getting to nested JSON attributes is a bit trickier. Say you want a
scope for all orders with a particular plan name - something like
`Order.with_plan_name("Silver")`

```ruby
scope :with_plan_name, ->(plan_name) { where("product::json #>> '{ plan,name }' = ?", plan_name) }
```

I got to these by looking through Stack Overflow posts, grokking the Postgres
docs on JSON functions and [this post](http://devblog.avdi.org/2014/01/31/playing-with-json-in-postgres/)
of Avdi Grimm's. As usual, you can't comment here but if you see an error or
want to offer a suggestion for improvement, please get in touch via email.
