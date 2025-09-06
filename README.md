# 🏠 Airbnb Analytics (SQL Project)

This project explores **Airbnb / Hotel Booking Analytics** using SQL.  
The goal is to analyze booking trends, host performance, guest behavior, pricing, and cancellation impacts.

---

## 📂 Dataset

The project uses a dummy Airbnb-style dataset with the following tables:

- **listings** → listing details (listing_id, host_id, city, cancellation_policy, etc.)  
- **bookings** → booking details (booking_id, guest_id, listing_id, booking_date, checkin_date, checkout_date, price_per_night, total_price, status)  
- **guests** → guest details (guest_id, guest_name, country)  
- **calendar** → availability & pricing for each date per listing  

---

## 🎯 Key Business Questions & Queries

### 1️⃣ Monthly Revenue Trend by City

```sql
SELECT 
    SUM(b.total_price) AS Total_Price,
    b.nights,
    AVG(b.price_per_night) AS Avg_Price,
    l.city,
    MONTHNAME(b.booking_date) AS Booking_Month
FROM bookings b 
LEFT JOIN listings l ON b.listing_id=l.listing_id
GROUP BY b.nights, city, b.booking_date
ORDER BY SUM(b.total_price) DESC;
➡️ Find revenue, average price, and bookings trend per city by month.


2️⃣ Top 5 Hosts by Revenue in 2023 + Cancellation Rate
SELECT 
    host_id,
    SUM(CASE WHEN status = 'confirmed' THEN total_price ELSE 0 END ) AS Revenue,
    COUNT(*) AS Total_Booking,
    SUM(CASE WHEN status='Cancelled' THEN 1 ELSE 0 END) AS Cancelled_Booking,
    ROUND(SUM(CASE WHEN status='Cancelled' THEN 1 ELSE 0 END) * 100.0/COUNT(*),2) AS Cancellation_Rate
FROM bookings
WHERE YEAR(booking_date)=2023
GROUP BY host_id
ORDER BY Revenue DESC
LIMIT 5;
➡️ Identifies high-performing hosts and measures reliability through cancellations.


3️⃣ Guests with More Than 2 Bookings + Total Spend
SELECT 
    b.guest_id,
    g.guest_name,
    COUNT(g.guest_id) AS No_of_Booking,
    SUM(b.total_price) AS Total_Revenue
FROM bookings b
LEFT JOIN guests g ON b.guest_id=g.guest_id
GROUP BY g.guest_name,b.guest_id
HAVING COUNT(g.guest_id)>=2
ORDER BY COUNT(g.guest_id) DESC;
➡️ Finds loyal guests and their revenue contribution.


4️⃣ Average Lead Time per Cancellation Policy
SELECT 
    booking_id,
    booking_date,
    checkin_date,
    cancellation_policy,
    CONCAT(DATEDIFF(checkin_date, booking_date), " Days") AS Lead_Time
FROM bookings
ORDER BY booking_date;
➡️ Measures how far in advance guests book under different cancellation policies.


5️⃣ Cancellation Impact on Revenue
SELECT 
    booking_id,
    booking_date,
    total_price,
    status,
    CASE WHEN status='Confirmed' THEN total_price ELSE 0 END AS Confirmed_Revenue,
    CASE WHEN status='Cancelled' THEN total_price ELSE 0 END AS Cancelled_Revenue
FROM bookings
GROUP BY booking_id, booking_date, total_price, status
ORDER BY total_price DESC;
➡️ Breaks down revenue lost due to cancellations.


6️⃣ Compare “Strict” vs “Flexible” Policies
SELECT 
    cancellation_policy,
    SUM(Confirmed_Revenue) AS Total_Confirmed,
    SUM(Cancelled_Revenue) AS Total_Cancelled,
    SUM(Total_Price) AS Total_Revenue,
    ROUND((SUM(Cancelled_Revenue) / SUM(Total_Price)) * 100, 2) AS Revenue_Lost_Pct
FROM (
    SELECT 
        cancellation_policy,
        total_price,
        CASE WHEN status = 'Confirmed' THEN total_price ELSE 0 END AS Confirmed_Revenue,
        CASE WHEN status = 'Cancelled' THEN total_price ELSE 0 END AS Cancelled_Revenue
    FROM bookings
) AS Cancellation
GROUP BY cancellation_policy
ORDER BY Revenue_Lost_Pct DESC;
➡️ Compares revenue protection between strict and flexible cancellation policies.


7️⃣ Weekday vs Weekend Pricing
SELECT
    ROUND(AVG(c.price),2) AS Avg_Nightly_Price,
    CASE 
        WHEN DAYOFWEEK(b.booking_date) IN (1,7) THEN "Weekend" 
        ELSE "Weekday" 
    END AS Day_Type
FROM bookings b 
LEFT JOIN calendar c ON b.listing_id=c.listing_id
GROUP BY Day_Type
ORDER BY Avg_Nightly_Price;
➡️ Analyzes pricing strategies between weekdays and weekends.


8️⃣ Top 10 Listings by Revenue in Last 12 Months
SELECT 
    b.listing_id,
    SUM(b.total_price) AS Total_Revenue,
    l.city
FROM bookings b
LEFT JOIN listings l ON b.listing_id=l.listing_id
WHERE booking_date BETWEEN '2024-01-01' AND '2024-12-31'
GROUP BY b.listing_id,l.city
ORDER BY Total_Revenue DESC
LIMIT 10;
➡️ Identifies top-earning listings.


9️⃣ Highest Revenue Months Across Cities
SELECT 
    MONTHNAME(booking_date) AS Months,
    SUM(total_price) AS Total_Revenue,
    city
FROM bookings b 
LEFT JOIN listings l ON b.listing_id = l.listing_id
GROUP BY Months, city
ORDER BY Total_Revenue DESC;
➡️ Finds peak seasons and most profitable cities.


📊 Key Insights

Some hosts generate very high revenue but also face higher cancellation rates.

Loyal guests (2+ bookings) are significant contributors to revenue.

Strict cancellation policies protect revenue better than flexible ones.

Weekends have higher average nightly prices.

Certain months & cities clearly dominate in revenue.


📬 Contact
👤 Mohit Sharma
📞 +91-9001563116
📧 mohit20195@gmail.com
