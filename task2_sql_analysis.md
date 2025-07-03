# Task 2: AI-Assisted SQL Query Generation

## Task Description
Use AI tools (ChatGPT, Cursor) to generate and refine SQL queries for sales data analysis in SQLite environment.

## Test Environment
- **Tool**: [SQLite Online](https://sqliteonline.com/)
- **Data Setup**:
```sql
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer TEXT,
    amount REAL,
    order_date DATE
);

INSERT INTO orders (customer, amount, order_date) VALUES
('Alice', 5000, '2024-03-01'),
('Bob', 8000, '2024-03-05'),
('Alice', 3000, '2024-03-15'),
('Charlie', 7000, '2024-02-20'),
('Alice', 10000, '2024-02-28'),
('Bob', 4000, '2024-02-10'),
('Charlie', 9000, '2024-03-22'),
('Alice', 2000, '2024-03-30');
```

## AI Prompting Strategy

### Task 1: Monthly Sales Volume
**Prompt:**
```
Using this input data [paste INSERT statement above], create a SQL query to calculate the total sales volume for March 2024, that could be executed in the SQLite environment.
```

**Expected Result**: 27,000

### Task 2: Top Customer Analysis
**Prompt:**
```
Using this input data [paste INSERT statement above], create a SQL query to find the customer who spent the most overall, that could be executed in the SQLite environment.
```

**Expected Result**: Alice (20,000)

### Task 3: Average Order Value
**Prompt:**
```
Using this input data [paste INSERT statement above], create a SQL query to calculate the average order value for the last three months, assuming that maximum order_date will be the start point for calculating the last three months, that could be executed in the SQLite environment.
```

**Expected Result**: 6,000

## AI Tool Comparison

### ChatGPT Approach
- Tends to use standard SQL with date range filtering
- Provides clear, portable solutions
- Good at explaining query logic

### Cursor AI Approach  
- May use SQLite-specific functions like `strftime()`
- Often provides more concise solutions
- Better integration with code editor workflow

## Key Prompting Tips

### 1. Environment Specification
- Always mention "SQLite environment"
- Include sample data in prompts
- Specify expected result format

### 2. Context Clarity
- Provide complete table schema
- Include sample data for verification
- Specify date handling requirements

### 3. Validation Strategy
- Test generated queries immediately
- Compare results across AI tools
- Verify performance with larger datasets

## Expected Query Patterns

### Date Filtering Options:
```sql
-- Standard approach
WHERE order_date >= '2024-03-01' AND order_date < '2024-04-01'

-- SQLite-specific
WHERE strftime('%Y-%m', order_date) = '2024-03'
```

### Aggregation Patterns:
```sql
-- Customer ranking
SELECT customer, SUM(amount) as total
FROM orders 
GROUP BY customer 
ORDER BY total DESC 
LIMIT 1;

-- Time-based averaging
SELECT AVG(amount) 
FROM orders 
WHERE order_date >= date('max_date', '-3 months');
```

## Success Metrics
- All queries execute without errors in SQLite Online
- Results match expected values exactly
- Queries are readable and maintainable