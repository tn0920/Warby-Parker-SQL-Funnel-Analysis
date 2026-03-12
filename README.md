<img width="1100" height="220" alt="image" src="https://github.com/user-attachments/assets/5f446099-4ab6-4e0b-a953-82aa88368ead" />

**Context:** Warby Parker is a transformative lifestyle brand with a lofty objective: to offer designer eyewear at a revolutionary price while leading the way for socially conscious businesses. Founded in 2010 and named after two characters in an early Jack Kerouac journal, Warby Parker believes in creative thinking, smart design, and doing good in the world — for every pair of eyeglasses and sunglasses sold, a pair is distributed to someone in need.

**Objective:** This project aims to analyze different Warby Parker’s marketing funnels in order to calculate conversion rates.

-------------------------------------------------------------------
**Step 0: Understand data structure**  
There are 4 tables: home_try_on, purchase, survey, quiz
<img width="825" height="587" alt="image" src="https://github.com/user-attachments/assets/7e937c35-34c7-4a26-9646-8c8cc5852bd3" />




**Step 1: Preview Data Tables**

SELECT *   
FROM home_try_on LIMIT 5;  
SELECT *   
FROM purchase LIMIT 5;  
SELECT *  
FROM quiz LIMIT 5;  
SELECT *  
FROM survey  
limit 5;  


<img width="986" height="394" alt="image" src="https://github.com/user-attachments/assets/82c67516-5f7a-47e1-8c74-605e6aeed70f" />

<img width="808" height="128" alt="image" src="https://github.com/user-attachments/assets/32f7782f-6e1b-4197-98fb-94d5f903a3ee" />

=> Shows the first 5 rows of each table to understand their structure.  

**Step 2: Analyze Survey Completion**

SELECT question,  
COUNT(response)  
FROM survey  
GROUP BY question;  
<img width="565" height="130" alt="image" src="https://github.com/user-attachments/assets/9eb04947-a072-4008-88ad-86e9d7cfe29b" />


=> Counts responses for each quiz question to find drop-off points.

**Step 3: Build Funnel Table**

SELECT DISTINCT q.user_id,h.user_id IS NOT NULL AS is_home_try_on, h.number_of_pairs, p.user_id IS NOT NULL AS is_purchase  
FROM quiz AS q  
LEFT JOIN home_try_on AS h  
  ON q.user_id = h.user_id  
LEFT JOIN purchase AS p  
  ON p.user_id = q.user_id  
LIMIT 10;

<img width="813" height="234" alt="image" src="https://github.com/user-attachments/assets/4d0cb8e6-01e5-41ce-8255-d861388ff590" />


=> Combines user data across funnel stages for analysis.

**Step 4: Calculate Conversion Rates by Number of Pairs**

WITH report AS (  
  SELECT DISTINCT q.user_id,  
         h.user_id IS NOT NULL AS is_home_try_on,  
         h.number_of_pairs,  
         p.user_id IS NOT NULL AS is_purchase 
         
  FROM quiz AS q  
  LEFT JOIN home_try_on AS h  
    ON q.user_id = h.user_id  
  LEFT JOIN purchase AS p  
    ON p.user_id = q.user_id  
)  

SELECT number_of_pairs,  
       COUNT(DISTINCT user_id) AS user_num,  
       SUM(is_home_try_on) AS try_on_num,  
       SUM(is_purchase) AS purchase_num,  
       ROUND(SUM(is_purchase)*100.0/SUM(is_home_try_on), 2) AS from_try_to_purchase  
       
FROM report  
GROUP BY number_of_pairs;  
<img width="824" height="86" alt="image" src="https://github.com/user-attachments/assets/697a058c-05aa-4c22-9639-a37fc84b5944" />


=> Shows conversion rates for users who tried 3 vs. 5 pairs.

**Step 5: Find Most Popular Quiz Styles**

SELECT style, COUNT(*) AS count
FROM quiz
GROUP BY style
ORDER BY count DESC;
<img width="576" height="77" alt="image" src="https://github.com/user-attachments/assets/dc00d5c7-40b3-4a10-9e06-6150ff8b8338" />


=> Identifies the most selected styles in the quiz.

**Step 6: Find Most Purchased Models**

SELECT model_name, COUNT(*) AS count  
FROM purchase  
GROUP BY model_name  
ORDER BY count DESC;  

<img width="600" height="145" alt="image" src="https://github.com/user-attachments/assets/3b8acc99-2c7d-4ea7-82d0-057e4cc36d94" />

=> Lists the most purchased frame models.
<img width="1012" height="99" alt="image" src="https://github.com/user-attachments/assets/cb2cb040-36e9-42d0-9136-7672d53e075d" />

**Step 7: Calculate Conversion Rate by Style**

WITH report as (SELECT DISTINCT q.user_id, h.user_id IS NOT NULL AS 'is_home_try_on', h.number_of_pairs, p.user_id IS NOT NULL AS 'is_purchase'  
FROM quiz as q  
LEFT JOIN home_try_on as h  
  ON q.user_id = h.user_id  
LEFT JOIN purchase as p  
  ON p.user_id = q.user_id)  
  
SELECT number_of_pairs,COUNT(DISTINCT user_id) as 'user_num', round(sum(is_purchase)*100/sum(is_home_try_on),2) as 'from_try_to_purchase'  
FROM report  
GROUP BY number_of_pairs  

<img width="723" height="81" alt="image" src="https://github.com/user-attachments/assets/94238c5b-5c61-4ff3-93e8-dc2aacc3c058" />

<img width="723" height="114" alt="image" src="https://github.com/user-attachments/assets/b3e6de16-cd5e-4487-aa42-143b66fb9b4b" />

------------------
**High Level Recommendations:**

- Offer 5 pairs for home try-on as the default, since it leads to higher purchase rates.  

- Focus marketing and inventory on the most popular styles and models found in the quiz and purchase data.  

- Highlight best-selling colors and frames in promotions.  

- Simplify or shorten the quiz to reduce drop-off, especially after the second question.  

- Use high-converting quiz results to personalize product suggestions for users.  



