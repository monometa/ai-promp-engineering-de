# Task 1: AI-Assisted PostgreSQL Query Optimization

\<explain analyze>

## Task Description
Use AI tools (ChatGPT, Cursor) to identify and fix performance bottlenecks in a PostgreSQL query that takes ~70s but needs to run under 15s.

## Initial Query
```sql
SELECT
    o.order_id,
    o.customer_id,
    SUM(CASE WHEN oi.status = 'FULFILLED' THEN oi.quantity * oi.unit_price ELSE 0 END) AS gross_sales,
    COALESCE(r.total_refund, 0) AS total_refund,
    c.iso_code AS currency
FROM orders o
LEFT JOIN order_items oi ON oi.order_id = o.order_id
LEFT JOIN (
    SELECT order_id, SUM(amount) AS total_refund
    FROM refunds
    WHERE created_at::date = CURRENT_DATE - 1
    GROUP BY order_id
) r ON r.order_id = o.order_id
LEFT JOIN currencies c ON c.currency_id = o.currency_id
WHERE o.created_at::date = CURRENT_DATE - 1
GROUP BY o.order_id, o.customer_id, r.total_refund, c.iso_code
ORDER BY gross_sales DESC;
```

## AI Prompting Strategy

### Step 1: Bottleneck Identification
**Prompt for ChatGPT:**
```
Can you spot possible bottlenecks in this query execution plan?

[Include SQL query + execution plan screenshot]

Focus on:
- Sequential scans
- Hash joins
- Date casting performance
- Subquery efficiency
```

### Step 2: Optimization Strategies
**Follow-up prompts:**

**A. Window Functions Approach:**
```
Rewrite this query using window functions to eliminate the self-aggregating subquery for refunds.
```

**B. Early Filtering:**
```
Filter early by moving status='FULFILLED' and the date predicate into CTEs to reduce data processing.
```

**C. Indexing Strategy:**
```
Create a partial index on order_items(created_at, status, order_id) WHERE status='FULFILLED' for this query.
```

### Step 3: Cursor IDE Integration
**Cursor Command (Ctrl + K):**
```
Rewrite to use a single window-function to pass over order_items (partition by order_id) and JOIN that result to orders. Eliminate the refunds sub-query by turning it into a window sum on refunds with a FILTER clause. Add EXPLAIN ANALYZE before and after.
```

## Expected AI Solutions

### Key Optimizations AI Should Identify:
1. **Date casting kills indexes** - `created_at::date` prevents index usage
2. **Subquery inefficiency** - Nested refunds query processes all data
3. **Late filtering** - Status filter applied after JOIN
4. **Missing indexes** - No specialized indexes for common patterns

### Expected Query Improvements:
- Range-based date filtering: `>= CURRENT_DATE - INTERVAL '1 day'`
- CTE structure for better readability and optimization
- Partial indexes for status-based filtering
- Window functions for complex aggregations

\</explain analyze>