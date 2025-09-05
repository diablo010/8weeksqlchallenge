# 🥑 Case Study #3 - Foodie-Fi

## 🌮 Solution - A. Customer Journey

Based off the 8 sample customers provided in the sample subscriptions table below, write a brief description about each customer’s onboarding journey.

**Answer:**

```sql
select s.customer_id, s.plan_id, p.plan_name, s.start_date
from plans p
join subscriptions s
on p.plan_id = s.plan_id
where s.customer_id in (1, 2, 11, 13, 15, 16, 18, 19);
```

<img width="388" height="460" alt="Screenshot 2025-08-15 134842" src="https://github.com/user-attachments/assets/8ab8055f-34af-481d-831f-069d024ebf0c" />

Based on the results above, we will look after 8 customers from the sample table.

**Customer 1:** This customer started their subscription journey with free trial on 1st Aug 2020. After the end of trial period, they shifted to basic monthly plan.

<img width="389" height="70" alt="Screenshot 2025-08-15 134900" src="https://github.com/user-attachments/assets/a785a058-45d1-41dd-a4ee-bc66aac50ac6" />

**Customer 2:** This customer started on 20th Sept 2020. Then, started with pro annual plan from 27th Sept 2020.

<img width="377" height="51" alt="Screenshot 2025-08-15 134914" src="https://github.com/user-attachments/assets/bb1a0a29-d63c-4b80-be5a-de674c2f3890" />

**Customer 11:** This customer started on 19th Nov 2020. Then, discontinued after the trial ended.

<img width="381" height="47" alt="Screenshot 2025-08-15 134946" src="https://github.com/user-attachments/assets/8f4af446-4972-4cea-8b11-b19c3d9b6218" />

**Customer 13:** This customer started on 15th Dec 2020. They shifted to basic monthly plan for around 3 months and later, subscribed to pro monthly plan.

<img width="385" height="71" alt="Screenshot 2025-08-15 135002" src="https://github.com/user-attachments/assets/637d241c-bc21-4325-8a8d-c8c59e0f3815" />

**Customer 15:** This customer started on 17th Mar 2020. They shifted to pro monthly plan for 1 month and later, discontinued.

<img width="381" height="62" alt="Screenshot 2025-08-15 135018" src="https://github.com/user-attachments/assets/77c46572-1eea-4b92-a937-a8b02a52db78" />

**Customer 16:** This customer started on 31st May 2020. They shifted to basic monthly plan for 4 months and later, subscribed to pro annual plan.

<img width="383" height="65" alt="Screenshot 2025-08-15 135037" src="https://github.com/user-attachments/assets/2760c088-00f0-4d8a-83bb-bb9f5e8edf36" />

**Customer 18:** This customer started on 6th July 2020. They shifted to pro monthly plan later.

<img width="384" height="45" alt="Screenshot 2025-08-15 135101" src="https://github.com/user-attachments/assets/0d8f9b37-ebac-4b28-b909-376bea8c5237" />

**Customer 19:** This customer started on 22nd June 2020. They shifted to pro monthly plan for 2 months and later, subscribed to pro annual plan.

<img width="387" height="73" alt="Screenshot 2025-08-15 135116" src="https://github.com/user-attachments/assets/8cb2bd94-33ea-4155-aa43-9dec2b11e8c2" />
