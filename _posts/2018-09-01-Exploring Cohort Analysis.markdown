---
layout: post
title:  "Exploring Cohort Analysis"
date:   2018-09-01 00:01:00 -0500
categories: tableau
published: true
---

## Tableau - Exploring Cohort Analysis

Simply put, cohort analysis is a technique for analyzing activity over time by a common characteristic.  Mostly used in sales and marketing, cohort analysis can be used in tasks such as analyzing customer loyalty, customer cost acquisition, marketing campaign effectiveness and to explore many other aspects of sales.

<b>Dataset</b>

I am using the superstore sales created by <a href="http://community.tableausoftware.com/people/MM." target="_blank">Michael Martin</a> found <a href="http://community.tableausoftware.com/docs/DOC-1236" target="_blank">here</a> or <a href="https://mtalavera.files.wordpress.com/2014/10/sample-superstore-sales-excel.xls" target="_blank">here</a>.  This excel file contains three sheets of which only the first one, Orders, will be used in this analysis.

<b>Case</b>

The store providing their sales data does monthly advertising campaigns and wants to track what impact these advertising campaigns have on the amounts of orders placed over time.  They want to use this information to evaluate their different campaigns and improve their efforts.

Given the superstore sales data and the requirements, lets present the number of orders placed per customer join date.  Presenting the number of orders per join date will show the effectiveness of advertising campaigns leading to such date.

<b>Tools</b>
<ul>
	<li><a href="https://www.oracle.com/database/index.html" target="_blank">Oracle Database</a></li>
	<li><a href="http://www.tableausoftware.com/public/community" target="_blank">Tableau Public</a></li>
</ul>
Any database server can be used to follow along.  The code used here can easily be revised to work on any vendor’s product like <a href="http://www.mysql.com/" target="_blank">MySQL</a>, etc.  For visualization purposes, Tableau can be easily changed replaced by <a href="http://www.libreoffice.org/" target="_blank">LibreOffice</a> or similar.

<!--more-->

<b>Analysis</b>

From the Orders Sheet, we are only going to use the following columns: Order Id, Order Date and Customer Name.  For further analysis, however, lets preserve all the data in our system by saving the contents of the Orders Sheet in a database table.

Lets appropriately call this table superstore_sales.  Also, lets execute our analysis for month/year to match the store’s advertising efforts.

1. First, lets fetch the orders, together with the order date and the customer name.

    ![Cohort 1]({{ "/assets/cohort/cohort1.jpg" | absolute_url }}) 
    
    <table border="0" width="600" cellspacing="0" cellpadding="2" bgcolor="#d8d8d8">
    <tbody><tr><td valign="top" width="400"><pre>
    select 
    	a.order_id
    	, to_char(a.order_date, 'YYYY-MM') order_date
    	, a.customer_name customer
    from
    	superstore_sales a
    group by 
    	a.order_id, a.order_date, a.customer_name
    order by 2,3;
    </pre></td></tr></tbody></table>
    <br/>

1. To this set, lets add the date each customer placed their first order in the store.

    ![Cohort 2]({{ "/assets/cohort/cohort2.jpg" | absolute_url }})
    
    <table border="0" width="600" cellspacing="0" cellpadding="2" bgcolor="#d8d8d8">
    <tbody><tr><td valign="top" width="400"><pre>
    select
    	a.order_id
    	, to_char(a.order_date, 'YYYY-MM') order_date
    	, a.customer_name customer
    	, to_char(min(b.order_date), 'YYYY-MM') join_date
    from
    	superstore_sales a
    inner join
    	superstore_sales b on a.customer_name = b.customer_name
    group by
    	a.order_id, a.order_date, a.customer_name
    order by 2,3;
    </pre></td></tr></tbody></table>
    <br/>

1. Interested in the effect of each month’s advertising campaign, lets create a campaign id for each month.  Basically, join date becomes campaign id.  Notice we dropped the customer name as well.  This is our join criteria only at this point.

    Here we see, for each order placed, the monthly campaign to attribute the order to. Neat.
    
    ![Cohort 3]({{ "/assets/cohort/cohort3.jpg" | absolute_url }}) 
    
    <table border="0" width="600" cellspacing="0" cellpadding="2" bgcolor="#d8d8d8">
    <tbody><tr><td valign="top" width="400"><pre>
    select
    	a.order_id
    	, to_char(a.order_date, 'YYYY-MM') order_date
    	, to_char(min(b.order_date), 'YYYY-MM') campaign_id
    from
    	superstore_sales a
    inner join
    	superstore_sales b on a.customer_name = b.customer_name
    group by
    	a.order_id, a.order_date, a.customer_name
    order by 2;
    </pre></td></tr></tbody></table>
    <br/>

1. Now, lets determine the number of months between the join date for each order's customer first order and the order date for each order.

    This is a mouthful; lets explain.  It is easier to see it if explained backwards.
    
    For each customer, calculate the number of months between their first order and every other order they have placed.
    
    There, this makes a bit more sense.
    
    Now we have a set of all orders by date by campaign and the number of months since the customer joined store.

    ![Cohort 4]({{ "/assets/cohort/cohort4.jpg" | absolute_url }}) 
    
    <table border="0" width="600" cellspacing="0" cellpadding="2" bgcolor="#d8d8d8">
    <tbody><tr><td valign="top" width="400"><pre>
    select
    	a.order_id
    	, to_char(a.order_date, ‘YYYY-MM') order_date
    	, round(months_between(a.order_date,min(b.order_date))) mon_diff
    	, to_char(min(b.order_date), ‘YYYY-MM') campaign_id
    from
    	superstore_sales a
    inner join
    	superstore_sales b on a.customer_name = b.customer_name
    group by
    	a.order_id, a.order_date
    order by 2, 4;
    </pre></td></tr></tbody></table>
    <br/>

1. Next up, we simply count orders by campaign id by number of months since customer joined store.

    ![Cohort 5]({{ "/assets/cohort/cohort5.jpg" | absolute_url }}) 
    
    <table border="0" width="600" cellspacing="0" cellpadding="2" bgcolor="#d8d8d8">
    <tbody><tr><td valign="top" width="400"><pre>
    select 
    	count(distinct order_id) order_count, 
    	mon_diff, 
    	campaign_id 
    from (
    	select
    		a.order_id
    		, round(months_between(a.order_date,min(b.order_date))) mon_diff
    		, to_char(min(b.order_date), ‘YYYY-MM') campaign_id
    	from
    		superstore_sales a
    	inner join
    		superstore_sales b on a.customer_name = b.customer_name
    	group by
    		a.order_id, a.order_date
    	order by 1
    	)
    group by 
    	mon_diff, campaign_id
    order by campaign_id;
    </pre></td></tr></tbody></table>
    <br/>

This is enough to satisfy our case; not bad.  Now we have the set we need to analyze sales by campaign id over time.  A short dive into Tableau (try Public) and we come out with an elegant way to view a lot of information at once.

Lets go over the visualization together.

The y axis represents the number of sales (order count), the x axis represents how many months ago (months diff) these sales occurred and the different colors each represent a campaign (campaign id).

Using the first month (ago) as an example, we can tell how many orders can be attributed to each campaign for the life of the data set.  Skipping to the last month, 48, we see how many orders were placed for the campaign that occurred back then.  Seeing only one color makes sense since that is our first advertising campaign.

<img style="margin:0 5px;display:inline;border-width:0;" title="Image(5)" src="http://mtalavera.files.wordpress.com/2014/10/image51.jpg" alt="Image(5)" width="644" height="433" border="0" />

The interactive version of the visualization above is <a href="https://public.tableausoftware.com/profile/mariotalavera#!/vizhome/CohortAnalysis/Sheet1" target="_blank">here</a> for you to try. There, you can see the actual numbers of orders per campaign, etc.

That’s it; thanks for reading.  This seems like a very effective way to analyze a large amount of sales data for insights into advertising performance.

This post only covers join date as the grouping characteristic; many other cases can be made where cohort analysis can be helpful. Similarly, many other visualizations can provide similar insight.

Maybe next time we can cover some of these; thanks for reading!