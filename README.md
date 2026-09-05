# Jumia Product Performance Analysis

An analysis carried out to explore pricing, discounts, ratings and customer engagement across a sample of Jumia product listings.

##  Project Objective

The goal of this project was to understand how **price, discounts, ratings, and customer feedback** contribute to product performance.

Since the dataset doesn't contain actual sales or units sold, I used **review count as a proxy for customer engagement**. It is important to note that review count is not a sales metric and the relationships found in this analysis do not translate to causality.

The project involved cleaning a messy scraped dataset, creating additional analytical fields, building PivotTables and charts and bringing the main insights together in an interactive dashboard.

##  Dataset

The dataset, `Excel_jumia_dataset.csv`, contains **115 scraped Jumia product listings** with six original fields:

- Product
- Current price
- Old price
- Discount
- Review
- Ratingd (misspelled in the original dataset)

The original dataset was kept unchanged in the `Raw_data` sheet so that the cleaning process could be traced back to the source.

##  Tools Used

- Git Bash
- Microsoft Excel
  
##  Data Cleaning

Before making any changes, I first carried out a data-quality audit of the raw dataset.

The audit found:

- 115 rows and 6 columns
- 58 missing Review values
- 58 missing Rating values
- 3 exact duplicate rows
- 6 repeated product names with different prices and reviews
- 57 negative review counts
- 1 product where both current and old prices were provided as ranges
- No invalid or out-of-range prices, discounts or ratings

### Key Cleaning Decisions

#### 1. Negative Review Counts

All 57 non-blank review counts were negative. Since a review count cannot realistically be negative, this appeared to be a systematic scraping or formatting issue.

I converted the values to their absolute values using `ABS()` and then pasted them as values to prevent the formulas from changing later.

#### 2. Missing Reviews and Ratings

There were 58 blank Review values and 58 blank Rating values.

I left these cells blank instead of replacing them with zero because **missing data and zero are not the same thing**. Treating missing reviews as zero, for example, would have  lowered the engagement figures.

#### 3. Price Range

One product had its current price listed as **KSh 1,620–1,980** and its old price as **KSh 2,200–3,200**.

Rather than dropping the row or simply using the lower value, I used the **midpoint** of each range as an estimate.

This allowed me to keep the product in the analysis while being transparent about the fact that the exact price was unavailable.

#### 4. Advertised vs. Calculated Discount

Using the midpoint prices for that product resulted in a calculated discount of **33%**, while the seller's advertised discount was **38%**.

I kept both values rather than overwriting the advertised discount. The difference is  as a result of using an estimated midpoint price.

#### 5. Duplicates

Three rows were exact duplicates across all six original fields, so they were removed.

However, six products had the same product name but different prices, discounts or review counts. These were kept because they could represent different sellers offering the same type of product.

#### 6. Text and Formatting

I also:

- Removed `"out of 5"` from the Rating values
- Converted ratings into numeric decimals
- Corrected `Ratingd` to `Rating`
- Standardized `old price` to `Old Price`
- Converted price values from text into numeric currency values
- Formatted prices as `KSh #,##0.00`

All cleaning decisions, affected rows and the reasoning behind them are explained in the **Data Dictionary** sheet.

##  Additional Fields

After cleaning the data, I added several fields to the `Cleaned_data` Excel Table (`tblProducts`) to make the dataset more useful for analysis.

### Discount Amount & Calculated Discount

These show the difference between the old and current prices and the corresponding calculated percentage discount.

The original **Advertised Discount** was also retained so the two could be compared.

### Rating Category

Products were grouped into:

- **Poor:** < 3
- **Average:** 3–4.5
- **Excellent:** > 4.5

### Discount Category

Discounts were grouped by these bands:

- **Low:** < 20%
- **Medium:** 20–40%
- **High:** > 40%

### Price Category

Products were classified as Low, Medium or High based on quartile thresholds calculated from the dataset.

- **Price Q1:** KSh 493
- **Price Q3:** KSh 1,669.50

### Data Status

This field shows whether each product has:

- Complete data
- Missing rating
- Missing review
- Missing both

This makes it easier to understand the level of data coverage behind the analysis.

### Engagement Strength

I defined **Strong Engagement** as a review count at or above the 75th percentile.

- **Review Q3:** 13 reviews

This provides a data-driven threshold rather than guessing a number.

### Combination Flags

I also created flags to identify products that fall into these combinations:

- High discount + low rating
- High discount + low engagement
- Many reviews + average rating
- Strong engagement + excellent rating

Where one of the required values was missing, the result was marked as **Missing**.

##  Analysis

### Descriptive Statistics

After cleaning, there were **112 valid products**.

Some key figures from the analysis:

- **Average current price:** KSh 1,186.89
- **Average discount:** 37%
- **Average rating:** 3.89
- **Total reviews:** 678
- **Most expensive product:** 32PCS Portable Cordless Drill Set sold at KSh 3,750
- **Least expensive product:** 3PCS Single Head Knitting Crochet Sweater Needle Set sold at KSh 38

### Relationship Analysis

I created three scatter plots with trendlines to explore whether price, discount, rating, and review count were related.

Only complete, non-blank pairs were used in the analysis through `FILTER()` helper ranges.

| Relationship | Correlation (r) | R² |
|---|---:|---:|
| Discount vs. Reviews | -0.111 | 0.012 |
| Rating vs. Reviews | 0.043 | 0.002 |
| Current Price vs. Rating | 0.110 | 0.012 |

All three relationships were **very weak**.

In simple terms, the variables don't appear to have a meaningful linear relationship in this  dataset. For example, products with bigger discounts did not always have more reviews.

### Product Rankings

I also created PivotTable-based rankings for:

- Top 10 products by Rating
- Bottom 10 products by Rating
- Top 10 products by Reviews
- Top 10 products by Discount
- High discount + low rating
- High discount + low engagement
- Many reviews + average rating
- Strong engagement + excellent rating

For ranking ties, I used review count as a secondary ranking for ratings and rating as a secondary ranking for reviews. Products with missing ratings were excluded from rating-based rankings.

##  Dashboard

The final dashboard brings the main analysis together on a **single screen**, making it easier to explore without scrolling.

It includes:

- **KPI cards**
  - Total Products
  - Average Price
  - Average Discount
  - Average Rating
  - Total Reviews

- **Top 10 charts**
  - Top 10 by Rating
  - Top 10 by Reviews
  - Top 10 by Discount

- **Scatter plots**
  - Discount vs. Reviews
  - Rating vs. Reviews
  - Price vs. Rating

- **Category charts**
  - Rating Mix
  - Discount Mix

- **Interactive slicers**
  - Rating Category
  - Discount Category
  - Price Category

The slicers are connected to the compatible PivotTables and charts, allowing users to explore the data from different angles.

I also included a refresh-date note.

##  Key Findings

### 1. Bigger discounts did not necessarily mean higher engagement

The correlation between discount and review count was only **-0.11**, suggesting almost no linear relationship.

So based on this dataset, sellers shouldn't assume that increasing a discount will automatically lead to more customer engagement.

### 2. Price and rating were barely related

The correlation between current price and rating was **0.11**.

This suggests that highly rated products were not consistently more expensive or cheaper than lower-rated products.

### 3. Some heavily discounted products still performed poorly

A few products combined high discounts with either low ratings or low engagement.

Instead of simply increasing the discount further, these products could be worth investigating from a **listing-quality perspective** for example, product presentation, images, descriptions or customer expectations.

### 4. Some products had both strong engagement and excellent ratings

These products stood out as potential examples of what is working well.

They could be looked into further to understand whether factors such as listing quality, product category, presentation or customer experience may be contributing to their performance.

###  Important Caveat

Correlation does **not** imply causation.

Also, review count is only being used as a **proxy for engagement**. It should not be interpreted as sales or revenue because the dataset does not contain sales, units sold or revenue figures.

##  Limitations

This analysis has a few important limitations:

- There is no sales, revenue or units sold data.
- One product's price was estimated using the midpoint of a price range.
- The 4.1–4.5 rating category boundary is a documented working assumption rather than an instructor-confirmed rule.
- The dataset is a relatively small sample of scraped product listings, so the findings should not automatically be generalized to all Jumia products.

##  Project Structure

```text
jumia-product-performance-dashboard/
├── README.md
├── data/
│   └── Excel_jumia_dataset.csv
├── dashboard/
│   └── jumia_product_dashboard.xlsx
└── images/
    ├── raw-data.png
    ├── cleaned-data.png
    ├── pivot-tables.png
    └── dashboard.png
```

## How to Use the Workbook
1. Open Excel_Jumia_Product_Analysis_Final.xlsx in Microsoft Excel.
2. Start with the Dashboard sheet to see the main findings.
3. Use the slicers to filter products by Rating Category, Discount Category, or Price Category.
4. Go to Raw_data to view the original, untouched dataset.
5. Go to Cleaned_data to view the cleaned tblProducts table and additional analytical fields.
6. Go to Analysis to explore the descriptive statistics, quartile thresholds, correlations and scatter plots.
7. Check the Data Dictionary for detailed explanations of the cleaning decisions, assumptions and thresholds.
8. If you make changes to the underlying data, right-click a PivotTable and select Refresh to update the analysis and dashboard.

## Conclusion
This project gave me an opportunity to work through a complete Excel data analytics workflow  from auditing and cleaning messy data to creating data enrichment fields, analyzing relationships, building PivotTables and communicating insights through an interactive dashboard.

It reminded me that good analysis isn't just about creating charts. Understanding the quality of the data, documenting assumptions and being careful about what the data can and cannot tell us is just as important.
