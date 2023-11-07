---
layout: post
title:  "The Query Object Pattern"
date:   2023-03-05 02:03:34 -0300
categories: query-object patterns rails ruby design
---

Query objects are objects that encapsulate complex database queries that spans across several tables.

### Benefits
- **Improved code organization**: Query objects help in separating complex SQL queries from ActiveRecord models, resulting in cleaner and more maintainable code.
- **Reusability**: By encapsulating complex queries within query objects, you can reuse them across different parts of your application, reducing code duplication.
- **Readability**: Query objects provide a clear and explicit interface for executing database queries, making it easier to understand and reason about the code.
- **Testability**: Since query objects have well-defined inputs and outputs, they can be easily unit tested, ensuring the reliability of your queries.

## The Problem
We already have a place for common SQL queries, which are Active Record Scopes. The problem of them is that they are too tied to a model. You can scope a query to, let's say, an Order, by state, by date, by purchaser, but pretty much all of them are attributes of an Order. 
So where should we put queries that spans across several models, and returns a custom and not-model related ResultSet?

## Enter Query Objects 
Suppose that we have the following rails models:

{% highlight ruby %}
class Order
  has_many :shipments
  has_many :inventory_units, through: :shipments
  belongs_to :user
end
{% endhighlight %}
{% highlight ruby %}
class LineItem 
  belongs_to :order
end
{% endhighlight %}

After each order is placed, we want to be able to count how many individual articles a user purchased, and block the order if they went certain threshold. Limits might be monthly or yearly. 

{% highlight ruby %}
class PurchasedItemsVerifier
  attr_reader :order

  def initialize(order)
    @order = order
  end

  def call
    return false if monthly > 100
    return false if yearly > 500
    true
  end

  # returns items purchased by the same email in the same month
  def monthly
    Order
      .joins(:line_items)
      .where(email: order.email)
      .where(created_at: Date.today.all_month)
      .count
  end

  # returns items purchased by the same email in the same year 
  def yearly
    Order
      .joins(:line_items)
      .where(email: order.email)
      .where(created_at: Date.today.all_year)
      .count
  end
end
{% endhighlight %}

Let's say that we want to test this logic (i'm using rspec and factory_girl here, factories not included in the example)

{% highlight ruby %}
describe ComplianceValidator do
  let(:order) { create(:completed_order) }
  let(:shipments) { create_list(:shipment, 2, order: order) }
  let(:inventory_units) { create_list(:inventory_units, 3, shipment: shipment) }

  subject { ComplianceValidator.new(order).call }

  it 'should return false when same email purchased more than 10 bottles in a month' do
    
  end

  it 'should return false when same email purchased more than 10 bottles in a month' do
  end

  it 'should return false when same email purchased more than 10 bottles in a month' do
  end

end
{% endhighlight %}
