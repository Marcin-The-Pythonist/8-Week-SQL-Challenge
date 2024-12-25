<h1>Case Study #1 - Danny's Diner</h1>
<img src="https://8weeksqlchallenge.com/images/case-study-designs/1.png">
<hr>
<h3>Context</h3>
Danny seriously loves Japanese food, so at the beginning of 2021, he decided to embark upon a risky venture and opened a cute little restaurant that sells his three favourite foods: sushi, curry, and ramen.
Danny’s Diner needs your assistance to help the restaurant stay afloat - it has captured some very basic data from its few months of operation. Still, it has no idea how to use its data to help them run the business.
<hr>
<h3>Business Objective</h3>
Improving the loyalty program and evaluating its results. 
<hr>
<h3>Data Scheme</h3>

![Danny's Diner](https://github.com/user-attachments/assets/41134560-47a3-4fb6-9d52-f1eb67df14bb)
<hr>
<h3>Business questions</h3>
<ol>
  <li>What is the total amount each customer spent at the restaurant?</li>
  <h5>Thought Process</h5>
  <ul>
    <li>Discover that customers' IDs and purchase information are in separate tables.</li>
    <li>Join those tables on the common column(product_id).</li>
    <li>Aggregate the results by customer.</li>
  </ul>
  <h5>Code</h5>
  ```
  SELECT sales.customer_id, SUM(menu.price) FROM dannys_diner.sales
  INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
  GROUP BY customer_id 
  ORDER BY customer_id
  ```
  <li>How many days has each customer visited the restaurant?</li>
  <li>What was the first item from the menu purchased by each customer?</li>
  <li>What is the most purchased item on the menu and how many times was it purchased by all customers?</li>
  <li>Which item was the most popular for each customer?</li>
  <li>Which item was purchased first by the customer after they became a member?</li>
  <li>Which item was purchased just before the customer became a member?</li>
  <li>What are the total items and amount spent for each member before they became a member?</li>
  <li>If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?</li>
  <li>In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?</li>
</ol>
