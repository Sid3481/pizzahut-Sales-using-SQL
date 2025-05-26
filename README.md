Basic:
1:- Retrieve the total number of orders placed.
Answer :-  select COUNT(order_id) as Total_Orders from orders;
 

2:- Calculate the total revenue generated from pizza sales.  JOIN    
Answer :- select 
round(sum(order_details.quantity * pizzas.price), 2) as total_sales
from order_details join pizzas 
on pizzas.pizza_id = order_details.pizza_id	

 

3:- Identify the highest-priced pizza.  JOIN
select pizza_types.name, pizzas.price
from pizza_types join pizzas 
on pizza_types.pizza_type_id = pizzas.pizza_type_id
order by pizzas.price desc;

 

4:- Identify the most common ordered. JOIN
Reference check of quantity from order order_details table :- 
select quantity, COUNT(order_details_id) as order_details_id
from order_details group by quantity;
----------------------------------------------------------------------------------------------------------------------------------
select pizzas.size, COUNT(order_details.order_details_id) as order_count
from pizzas join order_details
on pizzas.pizza_id = order_details.pizza_id
group by pizzas.size order by desc;
 
5:- List the top 5 most ordered pizza types along with their quantities. 3 table join
select pizza_types.name,
sum(order_details.quantity) as quantity
from pizza_types join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.name order by quantity desc;

 

Intermediate
5a :- Join the necessary tables to find the total quantity of each pizza ordered. (Same as question 5 )
Actual question – Using join
6:- Join the necessary tables to find the total quantity of each pizza category ordered. 
select pizza_types.category,
SUM(order_details.quantity) as quantity
from pizza_types join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.category order by quantity;

 
7:- Determine the distribution of orders by hour of the day.
select datepart(hour,[time]) as Hour, COUNT(order_id) as Total_Orders 
from orders
group by datepart(hour,[time])
order by Total_Orders desc;
 
8:- Join relevant tables to find the category-wise distribution of pizzas.
select category, COUNT(name) 
from pizza_types
group by category;

 

9:- Group the orders by date and calculate the average number of pizzas ordered per day.

select AVG(quantity) as Total_quantity from

(select orders.date, sum(order_details.quantity) as quantity
from orders 
join order_details on
orders.order_id = order_details.order_id
group by orders.date) as order_quantity;

 


10:- Determine the top 3 most ordered pizza types based on revenue.
select pizza_types.name,
sum(order_details.quantity * pizzas.price) as revenue
from pizza_types join pizzas
on pizzas.pizza_type_id = pizza_types.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.name
order by revenue desc;

 

Advanced:
11:- Calculate the percentage contribution of each pizza type to total revenue.
select pizza_types.category,
round(sum(order_details.quantity * pizzas.price) / (select 
round(sum(order_details.quantity * pizzas.price), 2) as Total_sales
from order_details 
join pizzas
on pizzas.pizza_id = order_details.pizza_id)*100,2) as revenue
from pizza_types join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.category
order by revenue desc;
 

12:- Analyze the cumulative revenue generated over time.
select date,
sum(revenue) over(order by date) as cum_revenue
from
(select orders.date,
sum(order_details.quantity * pizzas.price) as revenue
from order_details join pizzas
on order_details.pizza_id = pizzas.pizza_id
join orders
on orders.order_id = order_details.order_id
group by orders.date) as sales;

 

13:- Determine the top 3 most ordered pizza types based on revenue for each pizza category.
select name, revenue
from

(
select category, name, revenue,
RANK() over(partition by category order by revenue desc) as RN
from
(
select pizza_types.category,pizza_types.name,
sum((order_details.quantity) * pizzas.price) as revenue
from pizza_types join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.category, pizza_types.name) as A) as B 
where RN <= 3;
 
