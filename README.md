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
