# 🛍️ Customer Shopping Behavior Analysis

A comprehensive data analytics project that explores customer shopping patterns, purchasing behavior, and revenue insights using SQL queries, Python analysis, and Power BI visualizations.

## 📋 Project Overview

This project analyzes customer shopping behavior across multiple dimensions including demographics, purchase patterns, product preferences, and subscription status. The dataset contains 3,900 customer records with 18 different attributes spanning clothing, footwear, accessories, and outerwear categories.

### 🎯 Key Objectives
- Understand revenue generation by gender demographics
- Identify high-value customers with discount usage patterns
- Analyze product performance through review ratings
- Compare shipping options and their impact on spending
- Evaluate subscription impact on customer spending
- Segment customers based on purchase history
- Identify seasonal and geographical trends

---

## 📊 Dataset Overview

**File:** `customer_shopping_behavior.csv`

### Dataset Statistics
- **Total Records:** 3,900 customers
- **Attributes:** 18 columns
- **Date Range:** Comprehensive cross-seasonal data
- **Data Quality:** 99.05% complete (37 missing values in Review Rating)

### Key Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| Customer ID | Integer | Unique customer identifier |
| Age | Integer | Customer age (18-70 years) |
| Gender | Categorical | Male/Female |
| Item Purchased | Categorical | 25 different product types |
| Category | Categorical | Clothing, Footwear, Accessories, Outerwear |
| Purchase Amount (USD) | Numeric | $20-$100 purchase value |
| Location | Categorical | 50 US states |
| Size | Categorical | XS, S, M, L, XL |
| Color | Categorical | 25 different color options |
| Season | Categorical | Winter, Spring, Summer, Fall |
| Review Rating | Float | 2.5-5.0 star ratings |
| Subscription Status | Categorical | Yes/No |
| Shipping Type | Categorical | Standard, Express, Free, Next Day, Store Pickup, 2-Day |
| Discount Applied | Categorical | Yes/No |
| Promo Code Used | Categorical | Yes/No |
| Previous Purchases | Integer | 1-50 purchases |
| Payment Method | Categorical | 6 payment types (Card, PayPal, Cash, Venmo, etc.) |
| Frequency of Purchases | Categorical | Weekly, Monthly, Quarterly, Annually, etc. |

---

## 📂 Project Files

### 1. **SQL Analysis** (`customer_shopping_behavior.sql`)
Contains 10 comprehensive SQL queries for data insights:

#### Q1: Revenue by Gender
```sql
SELECT gender, sum(purchase_amount*previous_purchases) as total_revenue
FROM customer
GROUP BY gender;
```
**Insight:** Compares total revenue contribution between male and female customers.

#### Q2: Discount Users with Above-Average Spending
```sql
SELECT customer_id, discount_applied  
FROM customer
WHERE discount_applied = "Yes" 
AND purchase_amount >= (SELECT AVG(purchase_amount) FROM customer);
```
**Insight:** Identifies high-value customers who also use discounts.

#### Q3: Top 5 Products by Review Rating
```sql
SELECT DISTINCT item_purchased, ROUND(AVG(review_rating), 2)
FROM customer
GROUP BY item_purchased
ORDER BY avg(review_rating) DESC
LIMIT 5;
```
**Insight:** Reveals best-performing products by customer satisfaction.

#### Q4: Shipping Method Comparison
```sql
SELECT shipping_type, ROUND(AVG(purchase_amount), 2) AS avg_purchase_amnt
FROM customer
WHERE shipping_type IN("Standard", "Express")
GROUP BY shipping_type;
```
**Insight:** Analyzes purchase amount differences across shipping methods.

#### Q5: Subscription Impact Analysis
```sql
SELECT subscription_status, 
       COUNT(customer_id) AS customer_count,
       ROUND(avg(purchase_amount), 2) AS avg_spent,
       sum(purchase_amount) AS total_revenue
FROM customer
GROUP BY subscription_status;
```
**Insight:** Demonstrates whether subscribers spend more than non-subscribers.

#### Q6: Products with Highest Discount Rate
```sql
SELECT item_purchased, 
       ROUND(100.0 * SUM(CASE WHEN discount_applied = "Yes" THEN 1 ELSE 0 END) / count(*), 2) AS discount_rate
FROM customer
GROUP BY item_purchased
ORDER BY discount_rate DESC
LIMIT 5;
```
**Insight:** Identifies which products are most frequently purchased with discounts.

#### Q7: Customer Segmentation (New, Returning, Loyal)
```sql
WITH customer_type AS (
    SELECT customer_id, previous_purchases,
    CASE 
        WHEN previous_purchases = 1 THEN 'New'
        WHEN previous_purchases BETWEEN 2 AND 10 THEN 'Returning'
        ELSE 'Loyal'
    END AS customer_segment
    FROM customer
)
SELECT customer_segment, count(*) AS "Number of Customers" 
FROM customer_type 
GROUP BY customer_segment;
```
**Insight:** Segments customers into lifecycle categories for targeted marketing.

#### Q8: Top 3 Products per Category
```sql
WITH item_counts AS (
    SELECT category, item_purchased,
           COUNT(customer_id) AS total_orders,
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY COUNT(customer_id) DESC) AS item_rank
    FROM customer
    GROUP BY category, item_purchased
)
SELECT item_rank, category, item_purchased, total_orders
FROM item_counts
WHERE item_rank <= 3;
```
**Insight:** Identifies top sellers within each product category.

#### Q9: Repeat Buyers & Subscription Correlation
```sql
SELECT subscription_status,
       COUNT(customer_id) AS repeat_buyers
FROM customer
WHERE previous_purchases > 5
GROUP BY subscription_status;
```
**Insight:** Shows relationship between repeat purchase behavior and subscriptions.

#### Q10: Revenue by Age Group
```sql
SELECT age_group, SUM(purchase_amount) AS revenue_contribution
FROM customer
GROUP BY age_group;
```
**Insight:** Analyzes revenue generation across different age demographics.

---

### 2. **Python Notebook** (`customer_shopping_behaviour.ipynb`)
Comprehensive data analysis and preprocessing using Pandas and NumPy.

#### Key Operations:

**Data Loading & Exploration**
- Load CSV data into Pandas DataFrame
- Perform exploratory data analysis (EDA)
- Display basic statistics and data types

**Data Cleaning**
- Handle missing values in Review Rating (37 nulls)
- Fill missing ratings using median by category
- Verify data quality post-cleaning

**Feature Engineering**
```python
# Create age groups
labels = ["Young Adult", "Adult", "Middle-aged", "Senior"]
df["age_group"] = pd.qcut(df["age"], q=4, labels=labels)

# Map purchase frequency to days
frequency_mapping = {
    "Fortnightly": 14,
    "Weekly": 7,
    "Monthly": 30,
    "Quarterly": 90,
    "Bi-Weekly": 14,
    "Annually": 365,
    "Every 3 Months": 90
}
df['purchase_frequency_days'] = df["frequency_of_purchases"].map(frequency_mapping)
```

**Data Preprocessing**
- Normalize column names (lowercase, underscores)
- Rename complex column names for easier access
- Create derived features for analysis

#### Dataset Insights from Notebook
- **3,900 records** with **18 complete attributes** (after cleaning)
- **Average Age:** 44.07 years (Range: 18-70)
- **Average Purchase:** $59.76 (Range: $20-$100)
- **Average Review Rating:** 3.75/5.0
- **Average Previous Purchases:** 25.35 per customer
- **Male Dominance:** 68% male, 32% female customers
- **Most Common Category:** Clothing (1,737 records)
- **Most Popular Size:** M (1,755 records)
- **Most Popular Color:** Olive (177 records)
- **Most Used Shipping:** Free Shipping (675 orders)

---

### 3. **Power BI Dashboard** (`CUSTOMER_SHOPPING BEHAVIOR.pbix`)
Interactive visual dashboard for business intelligence and reporting.

#### Dashboard Features

**Sales & Revenue Analysis**
- Total revenue by gender
- Revenue trends by age group
- Revenue contribution by category
- Top performing products

**Customer Insights**
- Customer segmentation (New, Returning, Loyal)
- Subscription status impact analysis
- Repeat customer behavior patterns
- Average purchase by segment

**Product Performance**
- Top products by review rating
- Products with highest discount rates
- Category-wise product distribution
- Top 3 items per category

**Shipping & Logistics**
- Shipping method comparison (Standard vs Express)
- Average purchase by shipping type
- Discount penetration by shipping method

**Demographics**
- Age group distribution
- Gender-based spending patterns
- Location-based insights across 50 US states
- Seasonal purchasing trends

**Customer Behavior**
- Payment method preferences
- Purchase frequency patterns
- Discount and promo code usage
- Subscription status breakdown

---

## 🔍 Key Findings

### Revenue Insights
- **Total Revenue:** Driven primarily by high-frequency repeat buyers
- **Subscription Impact:** Subscribers show higher average spending
- **Gender Trends:** Analyze revenue contribution by gender demographics
- **Age Factor:** Different age groups have distinct spending patterns

### Customer Segmentation
- **New Customers:** 1 previous purchase
- **Returning Customers:** 2-10 previous purchases
- **Loyal Customers:** 11+ previous purchases

### Product Performance
- **Top-Rated Items:** Based on customer reviews (3.75/5.0 average)
- **Best Sellers:** By category and season
- **Discount Sensitivity:** High discount rates on certain products

### Operational Metrics
- **Average Purchase Amount:** $59.76
- **Shipping Preference:** Free Shipping is most popular
- **Payment Methods:** PayPal most frequently used (677 transactions)
- **Discount Adoption:** 57% of orders use discounts (2,223/3,900)

---

## 🛠️ Technologies Used

| Tool | Purpose |
|------|---------|
| **SQL** | Data querying and analysis |
| **Python** | Data cleaning, preprocessing, EDA |
| **Pandas** | Data manipulation and transformation |
| **NumPy** | Numerical computations |
| **Power BI** | Interactive dashboard & visualizations |
| **CSV** | Data storage format |

---

## 📈 Usage Instructions

### 1. Running SQL Queries
```bash
# Import the SQL file into your database
# Execute queries in sequence for progressive analysis
mysql -u username -p database_name < customer_shopping_behavior.sql
```

### 2. Running Python Analysis
```bash
# Install required packages
pip install pandas numpy jupyter

# Open and run the Jupyter notebook
jupyter notebook customer_shopping_behaviour.ipynb
```

### 3. Accessing Power BI Dashboard
- Open `CUSTOMER_SHOPPING BEHAVIOR.pbix` in Power BI Desktop
- Interact with visualizations and filters
- Export reports and share insights

---

## 📊 Analysis Highlights

### Q1: Revenue by Gender
Identifies which gender generates more revenue considering both purchase amount and frequency.

### Q2: High-Value Discount Users
Finds customers who use discounts but still maintain above-average spending patterns.

### Q3: Top Products by Rating
Lists the 5 most highly-rated products by customer reviews.

### Q4: Shipping Impact
Compares average purchase amounts for Standard vs Express shipping to understand customer behavior differences.

### Q5: Subscription Analysis
Shows comprehensive comparison between subscribers and non-subscribers in terms of:
- Customer count
- Average spending
- Total revenue contribution

### Q6-Q10: Advanced Analytics
- Discount penetration by product
- Customer lifecycle segmentation
- Category-wise top performers
- Repeat buyer subscription correlation
- Age-based revenue contribution

---

## 🎯 Business Recommendations

1. **Subscription Strategy:** Focus on converting high-value repeat customers to subscriptions
2. **Discount Management:** Optimize discount rates on products with high discount penetration
3. **Shipping Options:** Free shipping drives higher order volume; consider as default
4. **Product Strategy:** Invest in top-rated products and categories
5. **Age Targeting:** Tailor marketing campaigns to high-revenue age groups
6. **Geographic Focus:** Analyze and prioritize high-performing states

---

## 📝 Data Quality Notes

- **Total Records:** 3,900
- **Complete Records:** 3,863 (after cleaning)
- **Missing Values:** 37 in Review Rating (0.95%) - handled via median imputation
- **Data Consistency:** All categorical values validated
- **Duplicate Check:** No duplicate customer records identified

---

## 📄 License & Attribution

This project is part of customer analytics research. All data is synthesized for analytical purposes.

---

## 🤝 Contributing

To contribute improvements:
1. Analyze additional metrics
2. Enhance visualizations
3. Optimize SQL queries
4. Add new business questions

---

## 📧 Contact & Support

For questions about this analysis, please refer to the individual components:
- **SQL Queries:** See `customer_shopping_behavior.sql`
- **Python Code:** See `customer_shopping_behaviour.ipynb`
- **Dashboard:** See `CUSTOMER_SHOPPING BEHAVIOR.pbix`

---

**Last Updated:** May 15, 2026  
**Project Status:** Complete ✅
