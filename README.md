# Comprehensive Pizza Hut Sales Data Analysis with MySQL

**Introduction**

This project delves into analyzing sales data for a hypothetical pizza restaurant, PizzaHut, using MySQL. Through structured tables and SQL queries, the project evaluates sales trends, popular pizza choices, revenue metrics, and customer preferences, supporting data-driven business insights.

Key objectives of this analysis include:

* Counting total orders
* Calculating overall revenue
* Identifying top-selling pizzas and categories
* Analyzing customer ordering patterns
* Extracting valuable business insights through SQL queries

**Project Outline**

**Database Name:** **pizzahut**

**Data Tables**

**1) pizzas:** Details about each pizza, including type, size, and price.

**2) orders:** Records individual order details, such as date and time.

**3) order_details:** Contains specific information on items in each order, including the pizza ID and quantity.

**4) pizza_types:** Provides details on pizza types, categories, and ingredients.

**Table Breakdown**

**1. pizzas**

Describes all pizza items available:

* **pizza_id:** Unique identifier for each pizza.

* **pizza_type_id:** References the **pizza_types** table for type classification.

* **size:** Size of the pizza (small, medium, large).

* **price:** Cost of the pizza.

**2. orders**

Records each customer order:

* **order_id:** Unique identifier for the order.
* **date:** Date when the order was placed.
* **time:** Time of the order.

**3. order_details**

Provides details for each item within an order:

* **order_details_id:** Unique identifier for each order item.

* **order_id:** Links to the order in the **orders** table.

* **pizza_id:** References the pizza in the **pizzas** table.

* **quantity:** Number of pizzas ordered.

**4. pizza_types**

Defines types of pizzas and associated categories:

* **pizza_type_id:** Unique identifier for each type of pizza.

* **name:** Pizza type name (e.g., Margherita, Pepperoni).

* **category:** Pizza category (e.g., Vegetarian, Meat Lovers).

* **ingredients:** Key ingredients in each pizza type.

**SQL Queries and Key Insights**

**1. Count of Total Orders**

Determine the overall number of orders:

      SELECT COUNT(order_id) AS total_orders FROM orders;

**2. Total Revenue Generated from Sales**

Calculate total sales revenue:

      SELECT ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_revenue
      FROM order_details
      JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id;

**3. Highest Priced Pizza**

Find the highest-priced pizza in the inventory:

      SELECT pizza_types.name, pizzas.price
      FROM pizza_types
      JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
      ORDER BY pizzas.price DESC
      LIMIT 1;

**4. Most Frequently Ordered Pizza Size**

Identify the most common pizza size ordered:

      SELECT pizzas.size, COUNT(order_details.order_details_id) AS order_count
      FROM pizzas
      JOIN order_details ON pizzas.pizza_id = order_details.pizza_id
      GROUP BY pizzas.size
      ORDER BY order_count DESC;

**5. Top 5 Best-Selling Pizza Types**

List the five most popular pizza types by order volume:

      SELECT pizza_types.name, SUM(order_details.quantity) AS total_quantity
      FROM pizza_types
      JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
      JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
      GROUP BY pizza_types.name
      ORDER BY total_quantity DESC
      LIMIT 5;

**6. Orders by Pizza Category**

Aggregate the total quantity ordered per pizza category:

      SELECT pizza_types.category, SUM(order_details.quantity) AS total_quantity
      FROM pizza_types
      JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
      JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
      GROUP BY pizza_types.category
      ORDER BY total_quantity;

**7. Hourly Order Distribution**

Analyze the number of orders per hour of the day:

      SELECT HOUR(time) AS order_hour, COUNT(order_id) AS order_count
      FROM orders
      GROUP BY order_hour;

**8. Category-Wise Pizza Distribution**

Group pizza orders by category:

      SELECT category, COUNT(name) AS count
      FROM pizza_types
      GROUP BY category;

**9. Average Daily Pizza Orders**

Compute the average number of pizzas ordered per day:

      SELECT ROUND(AVG(quantity), 0) AS average_quantity
      FROM (
          SELECT orders.date, SUM(order_details.quantity) AS quantity
          FROM orders
          JOIN order_details ON orders.order_id = order_details.order_id
          GROUP BY date
      ) AS daily_orders;

**10. Top 3 Revenue-Generating Pizza Types**

Identify the three pizzas with the highest revenue:

      SELECT pizza_types.name, ROUND(SUM(order_details.quantity * pizzas.price), 2) AS revenue
      FROM pizza_types
      JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
      JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
      GROUP BY pizza_types.name
      ORDER BY revenue DESC
      LIMIT 3;

**11. Revenue Contribution by Pizza Category**

Calculate the revenue percentage contributed by each pizza category:

      SELECT pizza_types.category,
      ROUND(SUM(order_details.quantity * pizzas.price) / 
      (SELECT ROUND(SUM(order_details.quantity * pizzas.price), 2) FROM order_details
      JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id) * 100, 2) AS revenue_share
      FROM pizza_types
      JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
      JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
      GROUP BY pizza_types.category
      ORDER BY revenue_share DESC;

**12. Cumulative Revenue Over Time**

Analyze cumulative revenue over time to observe growth:

      SELECT order_date, SUM(revenue) OVER (ORDER BY order_date) AS cumulative_revenue
      FROM (
          SELECT orders.date AS order_date,
          SUM(order_details.quantity * pizzas.price) AS revenue
          FROM order_details
          JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
          JOIN orders ON orders.order_id = order_details.order_id
          GROUP BY orders.date
      ) AS daily_revenue;

**13. Top 3 Pizza Types by Revenue per Category**

Identify top 3 revenue-contributing pizza types within each category:

      SELECT name, revenue
      FROM (
          SELECT category, name, revenue, 
          RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rank
          FROM (
              SELECT pizza_types.category, pizza_types.name,
              SUM(order_details.quantity * pizzas.price) AS revenue
              FROM pizza_types
              JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
              JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
              GROUP BY pizza_types.category, pizza_types.name
          ) AS category_revenue
      ) AS ranked_revenue
      WHERE rank <= 3;

**Conclusion**

This project showcases how MySQL can be used to manage and analyze pizza sales data, drawing valuable insights from structured tables (**pizzas, orders, order_details,** and **pizza_types**). The SQL queries highlight crucial metrics like total sales, popular pizza choices, and category performance, all aiding strategic decision-making for PizzaHut.






