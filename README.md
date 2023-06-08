# Main-Assessment
#QUERY 1
WITH total_efood_metrics AS(
  SELECT `city`,SUM(`amount`)/COUNTIF(`order_id`>1000) AS `efood_basket`,COUNTIF(`order_id`>1000)/COUNT(DISTINCT(`user_id`)) AS `efood_freq`
  FROM `efood2022-389109.main_assessment.orders`
  GROUP BY `city`
  ORDER BY COUNT(`order_id`) DESC
),
total_Breakfast_metrics AS(
  SELECT `city`,`cuisine`,SUM(`amount`)/COUNTIF(`order_id`>1000) AS    `breakfast_basket`,   
  COUNTIF(`order_id`>1000)/COUNT(DISTINCT(`user_id`)) AS `breakfast_freq`,
  (COUNTIF(`order_id`>1000)/COUNT((`user_id`)>3)) /SUM(DISTINCT(`user_id`))  AS   
   `breakfast_users3freq_perc`, 
  COUNT(`order_id`) AS `orders`
  FROM `efood2022-389109.main_assessment.orders`
  WHERE `cuisine`='Breakfast'
  GROUP BY `city`,`cuisine`
  ORDER BY COUNT(`order_id`) DESC)

SELECT e.`city`,b.`breakfast_basket`,e.`efood_basket`,b.`breakfast_freq`,e.`efood_freq`,b.`breakfast_users3freq_perc`
FROM total_efood_metrics e
JOIN total_Breakfast_metrics b
ON e.`city`=b.`city`
ORDER BY b.`orders` DESC
LIMIT 5;


#QUERY 2

SELECT t.`city`, (t.`top_users` / o.`total_orders`) * 100 AS percentage
FROM (
  SELECT o.`city`, COUNT(*) AS `top_users`
  FROM `efood2022-389109.main_assessment.orders` AS o
  WHERE o.`user_id` IN (
    SELECT `user_id`
    FROM `efood2022-389109.main_assessment.orders`
    GROUP BY `user_id`
    ORDER BY COUNT(*) DESC
    LIMIT 10
  )
  GROUP BY o.`city`
) AS t
JOIN (
  SELECT `city`, COUNT(*) AS `total_orders`
  FROM `efood2022-389109.main_assessment.orders`
  GROUP BY `city`
) AS o ON t.`city` = o.`city`;
