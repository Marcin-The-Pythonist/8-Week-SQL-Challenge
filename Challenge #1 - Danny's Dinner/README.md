<h1>Case Study #1 - Danny's Dinner</h1>
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
  <h2><li>What is the total amount each customer spent at the restaurant?</li></h2>
  <h3>Thought Process💭</h3>
  <ul>
    <li>Discover that customers' IDs and purchase information are in separate tables.</li>
    <li>Join those tables on the common column(product_id).</li>
    <li>Aggregate the results by customer.</li>
  </ul>
  <h3>Code💻</h3>
  
  ```SQL
  SELECT sales.customer_id, SUM(menu.price) FROM dannys_diner.sales
  INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
  GROUP BY customer_id 
  ORDER BY customer_id
  ```
![image](https://github.com/user-attachments/assets/2802fdb1-0796-4bd5-9eca-0eb87686cad8)

  <h2><li>How many days have each customer visited the restaurant?</li></h2>
   <h3>Thought Process💭</h3>
  <ul>
    <li>Count visits by counting order dates per customer.r</li>
    <li>Realize that customers could order more than one product.</li>
    <li>Modify the approach by counting <b>unique</b> dates per customer.</li>
  </ul>
  <h3>Code💻</h3>
      
  ```SQL
  SELECT customer_id, COUNT(DISTINCT(order_date)) FROM dannys_diner.sales
  GROUP BY customer_id 
  ORDER BY customer_id
  ```
![image](https://github.com/user-attachments/assets/11ea8e9f-9226-4f5b-8711-14e7d8628cf2)

  <h2><li>What was the first item from the menu purchased by each customer?</li></h2>
    <h3>Thought Process💭</h3>
  <ul>
    <li>Define data needed to answer the question: customer IDs and dish name.</li>
    <li>Identify that data exist in separate tables - join them.</li>
    <li>Now the step that took me the most time - formulate a proper question and answer it. How do I extract the earliest order date for each customer? After some research, I discovered the beauty of the <b>RANK</b> and the <b>PARTITION BY</b> function. You can think of them like this: <b>look at each customer</b> like a separate table and then <b>bring order</b> to their orders. Finally, stack the <I>"Imagined tables"</i> onto each other and voilà!
      <li>I've also learned that <b>WHERE</b> statement is executed before <b>RANK</b>. Hence I contained <b>RANK</b> within the subquery to make sure it executes before <b>WHERE</b>.</li>
  </ul>
  <h3>Code💻</h3>

  ```SQL
  SELECT product_name, customer_id, order_date FROM 
  (
  SELECT order_date, product_name, customer_id, RANK() OVER(PARTITION BY customer_id ORDER BY order_date) FROM     
  dannys_diner.sales
  INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
  ) as merged
  WHERE RANK = 1
  ```
![image](https://github.com/user-attachments/assets/795ede8c-1523-4049-8db0-11c4e6d40419)

  <h2><li>What is the most purchased item on the menu and how many times was it purchased by all customers?</li></h2>
   <h3>Thought Process💭</h3>
   <ul>
     <li>Join the Sales and the Menu tables to obtain the names of the products ordered.</li>
     <li>Count records grouped by the name of the dish.</li>
   </ul>
  <h3>Code💻</h3>
  
  ```SQL
  SELECT COUNT(product_name) AS most_purchased, product_name FROM dannys_diner.sales
  INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id 
  GROUP BY product_name
  ORDER BY most_purchased DESC
  LIMIT 1
  ```
   ![image](https://github.com/user-attachments/assets/0e26eae8-e8ea-4a46-b0f5-9e65b6f04659)
  <h2><li>Which item was the most popular for each customer?</li></h2>
   <h3>Thought Process💭</h3>
  <ul>
    <li>First, I created a temporary table with the information I needed to answer the question.</li>
    <li>As in the previous question, I needed to find the first element within each group using the <b>RANK</b> function. This time, however, I <b>reversed RANK order</b> so that the most frequent elements are first. "Why?" you might ask. Because the <b>first element has a fixed value of 1, unlike the last element.</b><br>
Think of a scenario where one person buys only ramen and curry and the other person buys ramen, curry and sushi.<br>
For the person <b>no.1</b>, the last element is 2nd.<br>
For the person <b>no.2</b>, the last element would be the 3rd one.<br>
That's why it's easier to address the first element.</li>
    <li>Customer B appears 3 times in the table. It's because all the dishes are equally popular considering him.</li>
  </ul> 
  <h3>Code💻</h3>
  
  ```SQL
  WITH temp_tab AS(
  SELECT product_name, customer_id, COUNT(product_name) AS amount FROM dannys_diner.sales
  INNER JOIN dannys_diner.menu
  ON menu.product_id = sales.product_id
  GROUP BY customer_id, product_name)
  
  SELECT * FROM(
  SELECT RANK() OVER(PARTITION BY customer_id ORDER BY amount DESC), product_name, customer_id, amount FROM temp_tab
  )
  WHERE RANK = 1
  ```
  ![image](https://github.com/user-attachments/assets/1cee2589-a275-467f-9c1c-b329a9464b23)
  <h2><li>Which item was purchased first by the customer after they became a member?</li></h2>
   <h3>Thought Process💭</h3>
   <ul>
     <li>Thought how to separate orders before and after obtaining a membership.</li>
     <li>Joined the membership table and the sales table and calculated the difference between the membership start date and the order date. If the difference was negative or equal to 0 the transaction was made after obtaining the membership(Or on the same day).</li>
     <li>Ranked the resulting query by the time difference and partitioned by customer(Descending because the value closest to 0 is the earliest one). Also joined the menu table to get products' name.</li>
     <li>Wrapped the previous <b>RANK</b> query into a subquery so that I can extract the records <b>where RANK = 1.</b></li>
   </ul>
   <h3>Code💻</h3>
   
   ```SQL
/* JOIN data, select records where join_date - purchase_date <= 0 */
WITH temp_tab AS(
SELECT * FROM (SELECT AGE(join_date) - AGE(order_date) AS time_diff, sales.customer_id, sales.product_id FROM dannys_diner.sales
INNER JOIN dannys_diner.members
ON sales.customer_id = members.customer_id)
WHERE time_diff <= make_interval() 
)

SELECT * FROM(
SELECT time_diff, customer_id, product_name, RANK() OVER(PARTITION BY customer_id ORDER BY time_diff DESC) FROM (
SELECT * FROM temp_tab 
INNER JOIN dannys_diner.menu
ON temp_tab.product_id = menu.product_id
)
)
WHERE rank = 1
```
![image](https://github.com/user-attachments/assets/19ace48c-a5a2-497e-9e40-80d361abb1e9)

  <h2><li>Which item was purchased just before the customer became a member?</li></h2>
    <h3>Thought Process💭</h3>
    <ul>
      <li>The logic is almost the same as in the previous question. I used the previous query and modified it.</li>
      <li>The only thing that needs adjustment is the comparison part. Instead of a negative difference, I need only a positive one.</li>
      <li>As shown below, one record contains a month and negative days. It can be confusing to read but can be fixed with the <b>JUSTIFY_INTERVAL</b> function.</li>
    </ul>

   ![image](https://github.com/user-attachments/assets/d3b4697b-cb66-4962-8810-f84b818ea9cc)
 
  
  <h3>Code💻</h3>
    
  ```SQL
  WITH temp_tab AS(
  SELECT * FROM (SELECT JUSTIFY_INTERVAL(AGE(join_date) - AGE(order_date)) AS time_diff, sales.customer_id, sales.product_id FROM dannys_diner.sales
  INNER JOIN dannys_diner.members
  ON sales.customer_id = members.customer_id)
  WHERE time_diff >= make_interval() 
  )
  
  SELECT * FROM(
  SELECT time_diff, customer_id, product_name, RANK() OVER(PARTITION BY customer_id ORDER BY time_diff DESC) FROM (
  SELECT * FROM temp_tab 
  INNER JOIN dannys_diner.menu
  ON temp_tab.product_id = menu.product_id
  )
  )
  WHERE rank = 1
  ```
  ![image](https://github.com/user-attachments/assets/ca41150a-99f4-480e-9ea3-b3f13bef7bc4)

  <h2><li>What are the total items and amount spent for each member before they became a member?</li></h2>
   <h3>Thought Process💭</h3>
   <ul>
     <li>Consider data needed:<ul>
       <li>SUM of prices</li>
       <li>COUNT of products</li>
       <li>GROUPED BY customer IDs</li>
       <li>WHERE order_date < join_date</li>
     </ul>
    <li>Join all 3 tables as price, join_date, and order_date are in separate tables.</li>
    </li>
   </ul>
  
  <h3>Code💻</h3>
  
  ```SQL
  WITH temp_tab AS (SELECT * FROM dannys_diner.sales
  JOIN dannys_diner.menu ON menu.product_id = sales.product_id)
  
  SELECT SUM(price) AS total_spendings, COUNT(product_name) AS total_items, temp_tab.customer_id FROM temp_tab
  JOIN dannys_diner.members ON members.customer_id = temp_tab.customer_id
  WHERE order_date < join_date
  GROUP BY temp_tab.customer_id
  ```
![image](https://github.com/user-attachments/assets/08378891-23bd-410f-acf9-b8ee181b1b2d)

  <h2><li>If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?</li></h2>
     <h3>Thought Process💭</h3>
     <ul>
       <li>What data will I need:
       <ul>
         <li>The price of products</li>
         <li>Customers' IDs</li>
         <li>Names of dishes</li>
       </ul>
       </li>
       <li>Now I need to calculate points for each product. To calculate it I will use the CASE WHEN statement. Points are 10 times the price of curry and ramen and 20 times the price of sushi.</li>
       <li>Now I need to join the table with points to the table with customers' info and group the data by customers summing up points.</li>
     </ul>


```SQL
SELECT customer_id, 
SUM(CASE
	WHEN product_name = 'sushi' THEN price * 20
	ELSE price * 10
END) AS points
FROM dannys_diner.menu

INNER JOIN dannys_diner.sales
ON sales.product_id = menu.product_id

GROUP BY customer_id
ORDER BY customer_id
```
![image](https://github.com/user-attachments/assets/5cfb6497-fa76-4f1f-bf21-29c85c1e82ec)

  <h2><li>In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customers A and B have at the end of January?</li></h2>
   <h3>Thought Process💭</h3>
   <ul>
	   <li>I will use the query I created for the previous task and modify it so that it multiplies everything by 20.</li>
	   <li>Now I need to add the criteria:
	   <ul>
		   <li>The date is between the join date and the week later after the join date.</li>
		   <li>Only customers A and B</li>
		   <li>Only on January</li>
	   </ul>
	   </li>
	   <li>I need to modify the query further. Delete the aggregation and include dates.</li>
	   <li>Now I filter out any but A and B customers and ensure the date is January.</li>
	   <li>Now I realize I chose a bad approach because the query starts to be spaghetti-like. Nevertheless, I will continue and see this bring me =)</li>
	   <li>The next step would be making the query a temporary table and joining the members table.</li>
	   <li>Next, I have to undo <b>x2</b> multiplier for anything(except sushi because it's always x2) for any transaction that was more than a week later after the join date or before the join date.</li>
    </ul>
    <h3>Code💻</h3>

```SQL
WITH default_points AS (
SELECT customer_id, order_date, product_name,
CASE
	WHEN True THEN price * 20
END AS points
FROM dannys_diner.menu

INNER JOIN dannys_diner.sales
ON sales.product_id = menu.product_id

WHERE customer_id != 'C' AND EXTRACT(MONTH FROM order_date) = 1
 
ORDER BY customer_id
)

SELECT default_points.customer_id, 
SUM(CASE
	WHEN order_date - join_date > 6 OR order_date - join_date < 0 AND product_name != 'sushi' THEN points / 2
	ELSE points
END) AS points_updated
FROM default_points

INNER JOIN dannys_diner.members
ON members.customer_id = default_points.customer_id

GROUP BY default_points.customer_id
```
![image](https://github.com/user-attachments/assets/64edec17-76a4-4fce-a83f-cdef3d0bc8c5)
</ol>
<hr>
<h3>I'm always eager to learn! That's why I would appreciate your feedback. If you spot any mistakes or have any tips I'm happy to hear about them! You can reach out to me at <i>marcin.kubowicz.analyst@gmail.com</i></h3>
