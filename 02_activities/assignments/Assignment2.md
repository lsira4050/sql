# NAME: Laura Siracusa

# Assignment 2: Design a Logical Model and Advanced SQL

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

#### Submission Parameters:
* Submission Due Date: `February 1, 2025`
* Weight: 70% of total grade
* The branch name for your repo should be: `assignment-two`
* What to submit for this assignment:
    * This markdown (Assignment2.md) with written responses in Section 1 and 4
    * Two Entity-Relationship Diagrams (preferably in a pdf, jpeg, png format).
    * One .sql file 
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sql/pulls/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-two`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.

***

## Section 1:
You can start this section following *session 1*, but you may want to wait until you feel comfortable wtih basic SQL query writing. 

Steps to complete this part of the assignment:
- Design a logical data model
- Duplicate the logical data model and add another table to it following the instructions
- Write, within this markdown file, an answer to Prompt 3


###  Design a Logical Model

#### Prompt 1
Design a logical model for a small bookstore. 📚

At the minimum it should have employee, order, sales, customer, and book entities (tables). Determine sensible column and table design based on what you know about these concepts. Keep it simple, but work out sensible relationships to keep tables reasonably sized. 

Additionally, include a date table. 

There are several tools online you can use, I'd recommend [Draw.io](https://www.drawio.com/) or [LucidChart](https://www.lucidchart.com/pages/).

**HINT:** You do not need to create any data for this prompt. This is a conceptual model only. 

#### Prompt 2
We want to create employee shifts, splitting up the day into morning and evening. Add this to the ERD.

#### Prompt 3
The store wants to keep customer addresses. Propose two architectures for the CUSTOMER_ADDRESS table, one that will retain changes, and another that will overwrite. Which is type 1, which is type 2? 

**HINT:** search type 1 vs type 2 slowly changing dimensions. 

```
Your answer...

#Prompt 1
#Book table:
CREATE TABLE Book (
    BookID INTEGER PRIMARY KEY,
    Title TEXT,
    Author TEXT,
    ISBN TEXT,
    Price REAL,
    Publisher TEXT,
    PublicationDate TEXT -- Store date as TEXT in 'YYYY-MM-DD' format
)

#Customer Table
CREATE TABLE Customer (
    CustomerID INTEGER PRIMARY KEY,
    FirstName TEXT,
    LastName TEXT,
    Email TEXT,
    Phone TEXT,
    Address TEXT
)
#Date Table
CREATE TABLE Date (
    DateID INTEGER PRIMARY KEY,
    Date TEXT, -- Store date as TEXT in 'YYYY-MM-DD' format
    Day INTEGER,
    Month INTEGER,
    Year INTEGER,
    Quarter INTEGER
)
#Employee Table
CREATE TABLE Employee (
    EmployeeID INTEGER PRIMARY KEY,
    FirstName TEXT,
    LastName TEXT,
    Email TEXT,
    Phone TEXT,
    HireDate TEXT, -- Store date as TEXT in 'YYYY-MM-DD' format
    Position TEXT
)
#Order Table
CREATE TABLE "Order" (
	"OrderID"	INTEGER,
	"CustomerID"	INTEGER,
	"EmployeeID"	INTEGER,
	"OrderDate"	TEXT,
	"TotalAmount"	REAL,
	PRIMARY KEY("OrderID"),
	FOREIGN KEY("CustomerID") REFERENCES "Customer"("CustomerID"),
	FOREIGN KEY("EmployeeID") REFERENCES "Employee"("EmployeeID")
)
#Sales TAble
CREATE TABLE "Sales" (
	"SalesID"	INTEGER,
	"OrderID"	INTEGER,
	"BookID"	INTEGER,
	"Quantity"	INTEGER,
	"UnitPrice"	REAL,
	PRIMARY KEY("SalesID"),
	FOREIGN KEY("BookID") REFERENCES "Book"("BookID"),
	FOREIGN KEY("OrderID") REFERENCES ""
)
#Prompt 2
#Shift TAble
CREATE TABLE Shift (
    ShiftID INTEGER PRIMARY KEY,
    ShiftName TEXT,
    ShiftType TEXT, -- New column to indicate shift type (e.g., "Morning", "Evening")
    StartTime TEXT, -- Store time as TEXT in 'HH:MM' format
    EndTime TEXT -- Store time as TEXT in 'HH:MM' format
)
#EmployeeShift Table
CREATE TABLE EmployeeShift (
    EmployeeShiftID INTEGER PRIMARY KEY,
    EmployeeID INTEGER,
    ShiftID INTEGER,
    ShiftDate TEXT, -- Store date as TEXT in 'YYYY-MM-DD' format
    FOREIGN KEY (EmployeeID) REFERENCES Employee(EmployeeID),
    FOREIGN KEY (ShiftID) REFERENCES Shift(ShiftID)
)
#Add difference between morning and evening shifts
INSERT INTO Shift (ShiftName, ShiftType, StartTime, EndTime) VALUES
('Morning Shift', 'Morning', '08:00', '12:00'),
('Evening Shift', 'Evening', '13:00', '17:00');


#Prompt 3
#Type 1 Overwrite Changes
CREATE TABLE CustomerAddress (
    CustomerID INTEGER PRIMARY KEY,
    AddressLine1 TEXT,
    AddressLine2 TEXT,
    City TEXT,
    Province TEXT,
    PostalCode TEXT,
    Country TEXT,
    FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID)
);

#Type 2  Retain Changes
CREATE TABLE CustomerAddress (
    AddressID INTEGER PRIMARY KEY,
    CustomerID INTEGER,
    AddressLine1 TEXT,
    AddressLine2 TEXT,
    City TEXT,
    Province TEXT,
    PostalCode TEXT,
    Country TEXT,
    StartDate TEXT, -- Store date as TEXT in 'YYYY-MM-DD' format
    EndDate TEXT, -- Store date as TEXT in 'YYYY-MM-DD' format, NULL for current address
    FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID)
);

## Section 2:
You can start this section following *session 4*.

Steps to complete this part of the assignment:
- Open the assignment2.sql file in DB Browser for SQLite:
	- from [Github](./02_activities/assignments/assignment2.sql)
	- or, from your local forked repository  
- Complete each question


### Write SQL

#### COALESCE
1. Our favourite manager wants a detailed long list of products, but is afraid of tables! We tell them, no problem! We can produce a list with all of the appropriate details. 

Using the following syntax you create our super cool and not at all needy manager a list:
```
SELECT 
product_name || ', ' || product_size|| ' (' || product_qty_type || ')'
FROM product
```

But wait! The product table has some bad data (a few NULL values). 
Find the NULLs and then using COALESCE, replace the NULL with a blank for the first problem, and 'unit' for the second problem. 

**HINT**: keep the syntax the same, but edited the correct components with the string. The `||` values concatenate the columns into strings. Edit the appropriate columns -- you're making two edits -- and the NULL rows will be fixed. All the other rows will remain the same.

FROM product<div align="center">-</div>

-- Correcting null values
SELECT 
product_name || ', ' || COALESCE(product_size, '') || ' (' || COALESCE(product_qty_type, 'unit') || ')'

#### Windowed Functions
1. Write a query that selects from the customer_purchases table and numbers each customer’s visits to the farmer’s market (labeling each market date with a different number). Each customer’s first visit is labeled 1, second visit is labeled 2, etc. 

You can either display all rows in the customer_purchases table, with the counter changing on each new market date for each customer, or select only the unique market dates per customer (without purchase details) and number those visits. 

**HINT**: One of these approaches uses ROW_NUMBER() and one uses DENSE_RANK().

--Display all rows in customer_purchases table
SELECT 
    customer_id,
    market_date,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY market_date) AS visit_number
FROM 
    customer_purchases;

--Select only unique market dates per customer, and visits numbered
SELECT 
    customer_id,
    market_date,
    DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY market_date) AS visit_number
FROM 
    (SELECT DISTINCT customer_id, market_date FROM customer_purchases) AS unique_visits;


2. Reverse the numbering of the query from a part so each customer’s most recent visit is labeled 1, then write another query that uses this one as a subquery (or temp table) and filters the results to only the customer’s most recent visit.

SELECT 
    customer_id,
    market_date,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY market_date DESC) AS visit_number
FROM 
    customer_purchases;
	WITH ranked_visits AS (
    SELECT 
        customer_id,
        market_date,
        ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY market_date DESC) AS visit_number
    FROM 
        customer_purchases
)
SELECT 
    customer_id,
    market_date
FROM 
    ranked_visits
WHERE 
    visit_number = 1;

3. Using a COUNT() window function, include a value along with each row of the customer_purchases table that indicates how many different times that customer has purchased that product_id.

<div align="center">-</div>

SELECT 
    customer_id,
    product_id,
    market_date,
    COUNT(*) OVER (PARTITION BY customer_id, product_id) AS purchase_count
FROM 
    customer_purchases;
	
#### String manipulations
1. Some product names in the product table have descriptions like "Jar" or "Organic". These are separated from the product name with a hyphen. Create a column using SUBSTR (and a couple of other commands) that captures these, but is otherwise NULL. Remove any trailing or leading whitespaces. Don't just use a case statement for each product! 

| product_name               | description |
|----------------------------|-------------|
| Habanero Peppers - Organic | Organic     |

**HINT**: you might need to use INSTR(product_name,'-') to find the hyphens. INSTR will help split the column. 

<div align="center">-</div>

SELECT 
    product_name,
    TRIM(SUBSTR(product_name, INSTR(product_name, '-') + 1)) AS description
FROM 
    product
WHERE 
    INSTR(product_name, '-') > 0;
	

#### UNION
1. Using a UNION, write a query that displays the market dates with the highest and lowest total sales.

**HINT**: There are a possibly a few ways to do this query, but if you're struggling, try the following: 1) Create a CTE/Temp Table to find sales values grouped dates; 2) Create another CTE/Temp table with a rank windowed function on the previous query to create "best day" and "worst day"; 3) Query the second temp table twice, once for the best day, once for the worst day, with a UNION binding them. 

***
-- Step 1: Create a CTE to find total sales grouped by market dates
WITH SalesByDate AS (
    SELECT 
        market_date,
        SUM(cost_to_customer_per_qty * quantity) AS total_sales
    FROM 
        customer_purchases
    GROUP BY 
        market_date
),

-- Step 2: Create another CTE with a rank windowed function to rank the sales
RankedSales AS (
    SELECT 
        market_date,
        total_sales,
        RANK() OVER (ORDER BY total_sales DESC) AS sales_rank_desc,
        RANK() OVER (ORDER BY total_sales ASC) AS sales_rank_asc
    FROM 
        SalesByDate
)

-- Step 3: Query the second CTE twice, once for the best day, once for the worst day, with a UNION binding them
SELECT 
    market_date,
    total_sales,
    'Highest Sales' AS sales_type
FROM 
    RankedSales
WHERE 
    sales_rank_desc = 1

UNION

SELECT 
    market_date,
    total_sales,
    'Lowest Sales' AS sales_type
FROM 
    RankedSales
WHERE 
    sales_rank_asc = 1;


## Section 3:
You can start this section following *session 5*.

Steps to complete this part of the assignment:
- Open the assignment2.sql file in DB Browser for SQLite:
	- from [Github](./02_activities/assignments/assignment2.sql)
	- or, from your local forked repository  
- Complete each question

### Write SQL

#### Cross Join
1. Suppose every vendor in the `vendor_inventory` table had 5 of each of their products to sell to **every** customer on record. How much money would each vendor make per product? Show this by vendor_name and product name, rather than using the IDs.

**HINT**: Be sure you select only relevant columns and rows. Remember, CROSS JOIN will explode your table rows, so CROSS JOIN should likely be a subquery. Think a bit about the row counts: how many distinct vendors, product names are there (x)? How many customers are there (y). Before your final group by you should have the product of those two queries (x\*y). 

<div align="center">-</div>

#### INSERT
1. Create a new table "product_units". This table will contain only products where the `product_qty_type = 'unit'`. It should use all of the columns from the product table, as well as a new column for the `CURRENT_TIMESTAMP`.  Name the timestamp column `snapshot_timestamp`.

2. Using `INSERT`, add a new row to the product_unit table (with an updated timestamp). This can be any product you desire (e.g. add another record for Apple Pie). 

<div align="center">-</div>

#### DELETE 
1. Delete the older record for the whatever product you added.

**HINT**: If you don't specify a WHERE clause, [you are going to have a bad time](https://imgflip.com/i/8iq872).

<div align="center">-</div>

#### UPDATE
1. We want to add the current_quantity to the product_units table. First, add a new column, `current_quantity` to the table using the following syntax.
```
ALTER TABLE product_units
ADD current_quantity INT;
```

Then, using `UPDATE`, change the current_quantity equal to the **last** `quantity` value from the vendor_inventory details. 

**HINT**: This one is pretty hard. First, determine how to get the "last" quantity per product. Second, coalesce null values to 0 (if you don't have null values, figure out how to rearrange your query so you do.) Third, `SET current_quantity = (...your select statement...)`, remembering that WHERE can only accommodate one column. Finally, make sure you have a WHERE statement to update the right row, you'll need to use `product_units.product_id` to refer to the correct row within the product_units table. When you have all of these components, you can run the update statement.

*** 

## Section 4:
You can start this section anytime.

Steps to complete this part of the assignment:
- Read the article
- Write, within this markdown file, <1000 words.

### Ethics

Read: Boykis, V. (2019, October 16). _Neural nets are just people all the way down._ Normcore Tech. <br>
    https://vicki.substack.com/p/neural-nets-are-just-people-all-the

**What are the ethical issues important to this story?**

Consider, for example, concepts of labour, bias, LLM proliferation, moderating content, intersection of technology and society, ect. 


```
Your thoughts...
```
	Reading Vicki Boykis's article feels like pulling back the curtain on a magic trick. You know those impressive AI systems everyone's talking about? Turns out they're built on good old-fashioned human work - lots of it. And that raises some pretty big questions we need to think about. First up is the invisible workforce making all this possible. It's kind of like finding out your H&M t-shirt was hand-sewn - there's a whole world of human labor we never see. Take ImageNet, this massive database that powers many AI image recognition systems. Behind those sophisticated algorithms are countless people on Amazon Mechanical Turk, labeling images for pennies. Graduate students manually sorting through words. People doing the grunt work that makes AI look magical.
	Then there's the bias problem. When humans do the classifying and labeling, they bring their own perspectives and biases along for the ride. These biases get baked right into the foundation of AI systems. Remember that ImageNet art project where people could upload their photos and get AI labels back? Some of the results were pretty problematic - calling people "orphans" or "nerds" based on their appearance. Those weren't random glitches; they came from human-made classifications. The power structure here is fascinating too. At the top, you've got tech companies and famous researchers getting the glory. In the middle, academics doing the theoretical work. At the bottom, workers doing the endless task of labeling and sorting for minimal pay. Sound familiar? It's not so different from how fast fashion works - a few at the top profiting from many at the bottom.
	What's particularly tricky is how hidden all this is. When we can't see how these systems are built, it's hard to fix problems when they come up. It's like trying to fix a car when you can't open the hood. ImageNet's recent efforts to clean up biased classifications show just how complicated this can get. There's also the question of whose worldview gets encoded into these systems. When the foundational datasets are built mainly from Western perspectives, what does that mean for everyone else? It's like writing a world history book but only using sources from one country.
The academic-commercial pipeline adds another layer of complexity. Projects that start in universities often end up powering commercial AI systems. That's not necessarily bad, but it raises questions about responsibility. When your research project suddenly affects millions of people, who's accountable for the outcomes?
	Looking ahead, we've got some big questions to tackle. How do we make sure the people doing the foundational work are treated fairly? How do we build AI systems that don't just amplify existing biases? How do we make the human side of AI development more visible? What's striking is that these aren't really new problems - they're age-old questions about labor, power, and technology showing up in new ways. Just as we're still figuring out ethical manufacturing, we're now grappling with ethical AI development.
The bottom line? AI isn't as artificial as we might think. Behind every "smart" system is a network of very human decisions, work, and consequences. If we want to build better AI, we need to start by acknowledging and addressing the human element. After all, it's people all the way down.

