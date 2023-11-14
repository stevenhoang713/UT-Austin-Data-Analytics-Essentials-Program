# 🚙 Automotive Retail Analytics Case Study

## 📊 Business Outcomes  

### 1. What is the distribution of customers across states?

````sql
select state
     , count(customer_id) as total_customers
  from customer_t
 group by state
 order by total_customers desc;
````

**Answer:**

<img width="250" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/09164e03-8679-494e-bc0a-77db22d2073f.png">

Customer distribution across states is varied, with California and Texas having the highest numbers, and several states having lower customer counts.

***

### 2. What is the average rating in each quarter?

````sql
with rated_orders as (
       select order_id
	        , quarter_number
	        , case when customer_feedback like 'very bad' then 1
				   when customer_feedback like 'bad' then 2
				   when customer_feedback like 'okay' then 3
				   when customer_feedback like 'good' then 4 
		           else 5
		       end as rating
		 from order_t
		      )
select quarter_number
     , round(avg(rating),2) as avg_rating
  from rated_orders
 group by quarter_number
 order by quarter_number;
````

**Answer:**

<img width="250" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/27cb3f0a-848b-4a55-9522-fef3773022e2.png">

The average rating decreases progressively across quarters, starting at 3.55 in Q1 and declining to 2.40 in Q4.

***

### 3. Are customers getting more dissatisfied over time?

````sql
with quarterly_feedback_summary as (
	select quarter_number
         , sum(case when customer_feedback = 'very good' then 1 else 0 end) as very_good
		 , sum(case when customer_feedback = 'good' then 1 else 0 end) as good
         , sum(case when customer_feedback = 'okay' then 1 else 0 end) as okay
         , sum(case when customer_feedback = 'bad' then 1 else 0 end) as bad
         , sum(case when customer_feedback = 'very bad' then 1 else 0 end) as very_bad
         , count(customer_feedback) as total_feedback
	  from order_t
  group by quarter_number
  order by quarter_number
           ) 
select quarter_number
	 , round((very_good/total_feedback),2) as very_good
	 , round((good/total_feedback),2) as good
     , round((okay/total_feedback),2) as okay
     , round((bad/total_feedback),2) as bad
     , round((very_bad/total_feedback),2) as very_bad
  from quarterly_feedback_summary
 group by quarter_number
 order by quarter_number;
````

**Answer:**

<img width="400" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/5a4361d3-437c-4bce-89d5-6fee1ece9328.png">

Yes, customers appear to be getting more dissatisfied over time, as the proportion of "very_bad" ratings increases from 11% in Q1 to 31% in Q4, while "very_good" ratings decline.

***

### 4. Which are the top 5 vehicle makers preferred by the customer?

````sql
select p.vehicle_maker
     , count(o.customer_id) as total_customers
  from product_t p 
 right join order_t o 
	on p.product_id = o.product_id
 group by p.vehicle_maker
 order by total_customers desc
 limit 5;
````

**Answer:**

<img width="250" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/615d2232-8c28-44d1-870f-bfa18df216ce.png">

The top 5 vehicle makers preferred by customers, based on the number of vehicles, are Chevrolet (83), Ford (63), Toyota (52), Pontiac (50), and Dodge (50).

***

### 5. What is the most preferred vehicle make in each state?

````sql
select *
  from (
  select state
	   , vehicle_maker
	   , count(customer_id) as total_customers
	   , rank() over (partition by state order by count(customer_id) desc) as ranking
	from product_t
	join order_t using (product_id)
	join customer_t using (customer_id)
   group by state
		  , vehicle_maker
		 )   
	as _top_vehicle_makers
 where ranking = 1
 order by total_customers desc;
````

**Answer:**

<img width="400" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/23584ff5-31fb-4534-961d-6022f5305a18.png">


Chevrolet, Ford, Toyota, Pontiac, and Dodge emerges as the top five choices among customers and state. 

***

### 6. What is the trend of number of orders by quarters?

````sql
select quarter_number
     , count(order_id) as total_orders
  from order_t
 group by quarter_number
 order by quarter_number;
````

**Answer:**

<img width="250" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/ba124b89-dd36-4ed3-b7ce-77cc55cd047b.png">

The trend of total orders declined across quarters, with a notable decrease from 310 in Q1 to 199 in Q4.

***

### 7. What is the quarter over quarter % change in revenue?

````sql
with qtr_rev_summary as (
            select quarter_number
                 , round(SUM(quantity * vehicle_price),2) as revenue
			  from order_t
          group by quarter_number
	      order by quarter_number 
				)
select quarter_number
     , revenue
     , lag(revenue) over (order by quarter_number) as prev_revenue
     , round((revenue - lag(revenue) over (order by quarter_number)) / 
       lag(revenue) over (order by quarter_number),2) * 100 as quarterly_change
  from qtr_rev_summary
 group by quarter_number;
````

**Answer:**

<img width="400" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/93038445-8712-4028-b7e2-5768dec26b46.png">

There is a declining trend in quarter-over-quarter percentage change in revenue, with a 17% decrease from the first to the second quarter, an 11% decrease from the second to the third quarter, and a further 20% decrease from the third to the fourth quarter.

***

### 8. What is the trend of revenue and orders by quarters?

````sql
select quarter_number
     , round(sum(quantity*vehicle_price),0) as revenue
     , count(order_id) as total_orders
  from order_t
 group by quarter_number
 order by quarter_number;
````

**Answer:**

<img width="250" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/34068b2d-70d2-40a9-bd42-554313bd19db.png">

There is a consistent decline in both revenue and total orders across quarters, indicating a potential correlation in the decreasing performance of the business over time.

***

### 9. What is the average discount offered for different types of credit cards?

````sql
select c.credit_card_type
     , round(avg(o.discount),2) as average_discount
  from customer_t c 
  join order_t o 
    on c.customer_id = o.customer_id
 group by c.credit_card_type
 order by average_discount desc;
````

**Answer:**

<img width="250" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/89a7e352-cdcd-4d29-a03a-008a539a4aef.png">

Various credit cards receive different average discounts, with Laser having the highest at 0.64 and Diners Club International the lowest at 0.58.

***

### 10. What is the average time taken to ship the placed orders for each quarters?

````sql
select quarter_number
     , round(avg(datediff(ship_date, order_date)),0) as avg_shipping_time
  from order_t
 group by quarter_number
 order by quarter_number;
````

**Answer:**

<img width="250" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/77c92d6e-3c26-46bb-84ca-eb74cefb4cd1.png">

The average time taken to ship placed orders increases gradually from 57 in Q1 to 174 in the Q4.

***
