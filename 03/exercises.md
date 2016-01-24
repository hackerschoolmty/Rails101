## Hacker store

**Steps**

1. Fork hackerschool_store from [here]()
2. Run `bundle install` to install any dependency
3. Create Product, LineItem,  Order models as stated in [Model attributes](#model-attributes)
4. Complete user stories describe in [here](#user-stories)
5. Make a PR to master once you have finished

## Model Attributes

```
Order
  - name, string 

Product
  - name, string
  - price, decimal
  - status, string

LineItem
  - product_id, reference
  - quantity, decimal
  - order_id, reference
```

User
  - name
  - email

## User stories

- As a user the first page that I want to see is the list of products
- As a user I must be logged into the application so I can create an order
- As a user I want to create orders with multiple line items
- As a user I want to see a list of orders that I've made


### Validations:
- A Product status can be active or inactive
- Only active products can be added to the cart
- An order can contain multiple line items
- A Product can be added to cart only if is active
- Only logged users can create orders
- And order can't be deleted