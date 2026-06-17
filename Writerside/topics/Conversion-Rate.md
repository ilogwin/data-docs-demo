# Conversion Rate

## Definition

The percentage of users who completed a desired action (for example, a payment) out of all users who had the opportunity to do so.

## Formula

(COUNT(DISTINCT user_id WHERE action = 'completed') / COUNT(DISTINCT user_id)) * 100

## Data source

Users Dataset, Payments Dataset

## Owner

Product Analytics Team