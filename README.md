####### danny-s-diner
##### It is one of the case studies from the website 8weekssqlchallenge.com . In this I had answered all the questions using SQL . With all the answers in the form of queries.


### Below is the data set i have used and i have used mysql workbench to run all the queries.

CREATE SCHEMA dannys_diner;
use dannys_diner;

CREATE TABLE sales (
  customer_id VARCHAR(10),
  order_date DATE,
  product_id INT
);

INSERT INTO sales
  (customer_id, order_date, product_id)
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
 

CREATE TABLE menu (
  product_id INTEGER,
  product_name VARCHAR(5),
  price INTEGER
);

INSERT INTO menu
  (product_id, product_name, price)
VALUES
  (1, 'sushi', 10),
  (2, 'curry', 15),
  (3, 'ramen', 12);
  

CREATE TABLE members (
  customer_id VARCHAR(1),
  join_date DATE
);
INSERT INTO members
  (customer_id, join_date)
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
   
   
 ###  // Here I am Answering all the questions with sql queries as well.//
  
 ## --1. What is the total amount each customer spent at the restaurant?
  A-76,B-74,C-36
  WITH  amount AS(SELECT sales.customer_id,sales.product_id,menu.price FROM sales INNER 
  JOIN menu ON sales.product_id=menu.product_id)
  select customer_id, SUM(price)as total FROM amount group by customer_id;


## --2. How many days has each customer visited the restaurant?
   A-4Days,B-6Days,C-2Days
   select count(distinct order_date) as Visited_days ,customer_id from sales group by customer_id ;

##-- 3. What was the first item from the menu purchased by each customer?
    A-Sushi,B-Curry,C-Ramen
    select sales.*,menu.product_name from sales inner join menu on
    sales.product_id=menu.product_id group by customer_id having min(order_date) order by customer_id;

##-- 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
     most purchased item was Ramen.it was purchased 8 times.
     A-3times,B-2times,C-3times
     
     select sales.product_id,count(sales.product_id)as count,menu.product_name  from sales 
     inner join menu
     on sales.product_id=menu.product_id group by product_id order by count desc;   //most purchased item is Ramen.//
     
      select count( product_id) ,customer_id,product_id from sales where product_id=3 group by customer_id; // A-3times,B-2times,C-3times//
       
##-- 5. Which item was the most popular for each customer?
     


## -- 6. Which item was purchased first by the customer after they became a member?
   A-3(Ramen),B-1(Sushi),C is not a member
   
  select sales.*,members.join_date from sales inner join members 
  on sales.customer_id=members.customer_id where order_date>join_date group by customer_id ; 

## -- 7. Which item was purchased just before the customer became a member?


## -- 8. What is the total items and amount spent for each member before they became a member?
  A-25,B-40,C-36 (amount spent)
  A-2,B-3,C-3 (Total items )  // All before becoming a member.
  with sales_before_becoming_member as(  select sales.*,members.join_date from sales left join members 
  on sales.customer_id=members.customer_id where (order_date<join_date) or(join_date is null)) 
  select sales_before_becoming_member.customer_id,sum(menu.price)as amount_spent,
  count(sales_before_becoming_member.customer_id)  as total_items
  from sales_before_becoming_member inner join menu
  on sales_before_becoming_member.product_id=menu.product_id group by customer_id;


## -- 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
     A-860,B-940,C-360
     with new_price_table as(select sales.*,menu.product_name,menu.price 
      ,case when product_name='sushi' then 20*(price)
               else 10*(price) end as new_price
  from sales inner join menu 
  on sales.product_id=menu.product_id) select customer_id,sum(new_price) as total_points from new_price_table group by customer_id;
  

## -- 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
    A-8 points, B-4 points 
    A purchased 4 times after becoming a member.//2*4=8//
    B purchased 2 times after becoming a member.//2*2=4//
 
  select sales.*,members.join_date,2*count(product_id) as points from sales inner join members 
  on sales.customer_id=members.customer_id where (order_date>=join_date) and (month(order_date)=1)  group by customer_id;
  
