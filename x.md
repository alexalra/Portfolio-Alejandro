

## Questions

Let's ask some questions to explore that database and see how the deliveries of this restaurant are going.

1. Which recipe generated more revenues? 

<details>

  <summary>Answer</summary>
  

```
BBQ Feast
```
Code

```sql

SELECT
  recipe.name, SUM(orders.price)
FROM
  recipe
JOIN
  orders 
ON
  recipe.id = orders.recipe_id
GROUP BY
  1
ORDER BY
  2 DESC;
```
![image](https://github.com/alexalra/Portfolio-2/assets/78654579/1d309813-3fa4-4285-8bf9-27bdefa8538a)

</details>


2. Who is the customer that is allergic to fish and placed an order with a recipe containing some sort of fish? 

<details>

  <summary>Answer</summary>
  

```
It was Geoff, who ordered Po boy, that contains crawfish.
```
Code

```sql

SELECT
  customer.name,
  recipe.name,
  customer.allergens,
  recipe.ingredients
FROM
  recipe
JOIN
  orders 
ON
  recipe.id = orders.recipe_id
JOIN
  customer
ON
  orders.customer_id = customer.id
WHERE
  recipe.ingredients LIKE '%fish%' AND customer.allergens = 'fish';

```
![image](https://github.com/alexalra/Portfolio-2/assets/78654579/4dcc7283-5149-433a-ac5b-7e80a5a55247)

</details>

3. How much was every delivery?

<details>

  <summary>Answer</summary>
  

```
The orders.price is always higher than recipe.price. That is because orders.price includes the cost of the delivery. To calculate exactly how much every customer paid for the delivery we will deduct the recipe.price from every order.price and round the result. 
```
Code

```sql

SELECT
  orders.id,
  ROUND(orders.price - recipe.price,2) AS DELIVERY_PRICE
FROM
  recipe
JOIN
  orders
ON
  recipe.id = orders.recipe_id;


```
![image](https://github.com/alexalra/Portfolio-2/assets/78654579/30fe6f67-8060-4a91-8807-eae0d2067279)

</details>

4. Which is the name of the customer that gave the highest rating?

<details>

  <summary>Answer</summary>
  

```
It was Inna, with 10. 
```
Code

```sql

SELECT
  customer.name,
  rating.rating
FROM
  customer
JOIN
  customer_address
ON
  customer.id = customer_address.customer_id
JOIN
  rating
ON
  customer_address.customer_id = rating.customer_id
ORDER BY
   2 DESC
LIMIT
  1;


```
![image](https://github.com/alexalra/Portfolio-2/assets/78654579/2dd4bcfd-0303-4c4c-ae05-b29f81aa3687)

</details>

5. Which customers requested food/recipes containing ingredients they are allergic to?

<details>

  <summary>Answer</summary>
  

```
At the beginning, I run the code below and it was giving me a erroneous result. It was always missing one of the customers, Juan, who is allergic to pork. For some reason, it was not being captured. Probably because WHERE statement is case sensitive and while the recipe had 'Pork belly' the customer was allergic to 'pork'.
```
Code

```sql

SELECT
  customer.name,
  recipe.ingredients,
  customer.allergens
FROM
  recipe
JOIN
  orders ON recipe.id = orders.recipe_id
JOIN
  customer ON customer.id = orders.customer_id
WHERE
  recipe.ingredients LIKE '%' || customer.allergens || '%'

```
![image](https://github.com/alexalra/Portfolio-2/assets/78654579/acc0bc7d-0dbd-4bd3-b35e-c5eb23caa109)

  <summary>Answer</summary>
  

```
I sorted this issue by using LOWER before the column where the values were affected by the capital letters.

3 customers requested food containing ingredients they are allergic to.

Geoff, Juan and Inna
```
Code

```sql

SELECT
  customer.name,
  recipe.ingredients,
  customer.allergens
FROM
  recipe
JOIN
  orders ON recipe.id = orders.recipe_id
JOIN
  customer ON customer.id = orders.customer_id
WHERE
  LOWER(recipe.ingredients) LIKE '%' || customer.allergens || '%'

```
![image](https://github.com/alexalra/Portfolio-2/assets/78654579/5006fe0a-b09d-4ed9-8039-9d0d09740864)


