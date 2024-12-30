<h1>Case Study #2 - Pizza Runner🍕</h1>

![image](https://github.com/user-attachments/assets/bee175e7-c141-4919-bd57-f27af3e47fd8)
<hr>
<h2>Context</h2>
Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.
<hr>
<h2>Data Scheme</h2>

![image](https://github.com/user-attachments/assets/da7788ea-f41e-48fe-9518-0eb78c565b82)

<h2>Data Cleaning and transformation🧹</h2>
<h3>Tasks and issues to address</h3>
<ol>
  <li>Null values in the <code>runner_orders, customer_orders, </code> tables.</li>
  <li>Create a CTE to make further operations easier.</li>
</ol>

<hr>
<h2>Solutions❓</h2>

<h2>Pizza Metrics</h2>
<ol>
  <h3><li>How many pizzas were ordered?</li></h3>
  <h3>Code💻</h3>
  
  ```SQL
  SELECT COUNT(order_id) FROM pizza_runner.runner_orders 
  ```

  <h3>results🔢</h3>
  
  ![image](https://github.com/user-attachments/assets/f9e3b6bd-e3b4-444c-a20e-02d961a84777)
  <ul>
  <li>14 pizzas were ordered in total.</li>
  </ul>
  <hr>

  <h3><li>How many unique customer orders were made?</li></h3>
  <h3>Code💻</h3>
  
  ```SQL
  SELECT COUNT(DISTINCT order_id) FROM pizza_runner.customer_orders
  ```

  <h3>results🔢</h3>
  
  ![image](https://github.com/user-attachments/assets/56563da0-de57-46d4-b7c9-45aa8aa13ae2)
  <ul>
    <li>There were 10 unique orders in total.</li>
  </ul>
  <hr>
  <h3><li>How many successful orders were delivered by each runner?</li></h3>
 <ul>
 </ul>
  <li>How many Vegetarian and Meatlovers were ordered by each customer?</li>
  <li>What was the maximum number of pizzas delivered in a single order?</li>
  <li>For each customer, how many delivered pizzas had at least 1 change and how many had no changes?</li>
  <li>How many pizzas were delivered that had both exclusions and extras?</li>
  <li>What was the total volume of pizzas ordered for each hour of the day?</li>
  <li>What was the volume of orders for each day of the week?</li>
</ol>
