# Retail Banking Customer Churn & Support Optimization

## Project Overview
This project bridges 5 years of BPO customer support team leadership with retail banking data analytics. The goal is to analyze how customer service operational performance (ticket volumes and resolution speeds) intersects with customer financial profiles to influence account churn at a global retail bank.

---

## Analytical Phase 1: Operational Baseline Analysis

![Operational Baseline Chart](Churn_Chart.jpeg)

I executed an exploratory analysis to evaluate if ticket volume scales linearly with customer churn rates. 

### SQL Query:
```sql
SELECT 
    Support_Tickets_Raised,
    COUNT(CustomerId) AS total_customers,
    SUM(Exited) AS churned_customers,
    ROUND(AVG(Avg_Resolution_Time_Mins), 1) AS avg_resolution_time,
    ROUND((SUM(Exited) / COUNT(customerId)) * 100, 2) AS churn_rate
FROM bank_churn
GROUP BY Support_Tickets_Raised
ORDER BY Support_Tickets_Raised DESC;
```

### Empirical Findings:

| Tickets Raised | Total Customers | Churned Customers | Avg Resolution Time (Mins) | Churn Rate (%) |
|----------------|-----------------|-------------------|----------------------------|----------------|
| 5              | 1,633           | 342               | 31.9                       | 20.94%         |
| 4              | 1,678           | 347               | 31.7                       | 20.68%         |
| 3              | 1,699           | 338               | 31.2                       | 19.89%         |
| 2              | 1,684           | 341               | 32.0                       | 20.25%         |
| 1              | 1,617           | 331               | 31.3                       | 20.47%         |
| 0              | 1,689           | 338               | 31.3                       | 20.01%         |

**Analysis**: The exploratory analysis revealed a completely uniform distribution. Both customer attrition (~20%) and resolution times (~31-32 mins) remain entirely flat across all support levels. This demonstrates data skepticism; ticket volume alone does not drive churn, indicating that the bank must segment accounts by financial risk rather than uniform support volume.

---

## Analytical Phase 2: High-Value At-Risk Customers
I isolated active clients holding significant capital who are experiencing high support friction, generating an actionable revenue protection list.

### SQL Query:
```sql
SELECT customerId,
balance,
support_tickets_raised,
Avg_Resolution_Time_Mins
FROM bank_churn
WHERE Exited = 0
AND balance > 100000
AND support_tickets_raised >= 3
ORDER BY balance DESC;
```

### Empirical Findings:
* **The Insight**: This query successfully isolated high-net-worth clients currently caught in repeating support loops who have not yet left the bank.
* **Top Account Danger**: The highest-value account identified holds a massive balance of **$221,532.80** with 3+ pending or repeated support tickets. 

---

## Analytical Phase 3: Customer Engagement Analysis
I cross-referenced product ownership (`HasCrCard`) with active membership (`IsActiveMember`) to uncover hidden structural loyalty drivers.

### SQL Query:
```sql
SELECT IsActiveMember,
HasCrCard,
COUNT(CustomerId),
ROUND(SUM(Exited) / COUNT(CustomerId)* 100, 2) AS churn_rate
FROM bank_churn
GROUP BY IsActiveMember,
HasCrCard;
```

### Empirical Findings:

| IsActiveMember | HasCrCard | Total Customers | Churn Rate (%) |
|----------------|-----------|-----------------|----------------|
| 1 (Active)     | 1 (Yes)   | 3,607           | 13.36%         |
| 1 (Active)     | 0 (No)    | 1,544           | 16.39%         |
| 0 (Inactive)   | 1 (Yes)   | 3,448           | 27.32%         |
| 0 (Inactive)   | 0 (No)    | 1,401           | 25.70%         |

**Analysis**: Holding a credit card does not secure loyalty. In fact, inactive credit card holders represent the bank's highest risk tier (**27.32% churn rate**). Customer activity status (`IsActiveMember`) is the actual psychological safety mechanism keeping customers from jumping ship.

---

## BPO-Driven Executive Business Recommendations
Combining my 5 years of BPO team leadership with this data, I recommend three strategic operational solutions:
1. **Asset-Weighted Priority Support Routing**: Abandon standard first-in, first-out queues. Implement an automated CRM rule where accounts holding balances >$100,000 with 2+ open tickets automatically bypass tier-1 and route straight to a specialized retention desk.
2. **Re-engagement Campaigns for At-Risk Cardholders**: Target the inactive credit card segment (27.32% churn) with specialized cash-back incentives or targeted usage offers to stimulate active status and cut their attrition risk in half.
3. **Operational Capacity Guardrails**: Use the stable 31-minute average resolution handling time established in Phase 1 as a definitive workforce management baseline to structure staffing requirements during peak volume cycles.
