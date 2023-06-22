---
layout: post
title:  "The Query Object Pattern"
date:   2023-03-05 02:03:34 -0300
categories: jekyll update
---

Suppose that we have the following code:

{% highlight ruby %}
class Order
  has_many :shipments
  has_many :inventory_units, through: :shipments
end
{% endhighlight %}
{% highlight ruby %}
class Shipment
  belongs_to :order
  has_many :inventory_units
end
{% endhighlight %}
{% highlight ruby %}
class InventoryUnit
  belongs_to :shipment
end
{% endhighlight %}

{% highlight ruby %}
class ComplianceValidator
  def initialize(order)
    @order = order
  end

  def call
    monthly_items_count = 
      Order
        .joins(:inventory_units)
        .completed
        .where(email: @order.email)
        .where(created_at: Date.today.all_month)
        .count(:inventory_units)

    yearly_items_count = 
      Order
        .joins(:inventory_units)
        .completed
        .where(email: @order.email)
        .where(created_at: Date.today.all_year)
        .count(:inventory_units)

    return false if monthly_items_count > 10
    return false if yearly_items_count > 50
    true
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
