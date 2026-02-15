/* 
1. My partner and I want to come by each of the stores in person and meet the managers. 
Please send over the managers’ names at each store, with the full address 
of each property (street address, district, city, and country please).  
*/ 
-- Q1.
SELECT  S.first_name,
        S.last_name,
        A.address,
        A.district,
        CI.city,
        CO.country
FROM store AS ST
     INNER JOIN staff AS S
               ON ST.manager_staff_id = S.staff_id
     INNER JOIN address AS A
               ON A.address_id = ST.address_id
     INNER JOIN city AS CI
               ON CI.city_id = A.city_id
	 INNER JOIN country AS CO
               ON CO.country_id = CI.country_id;
      
/*
2.	I would like to get a better understanding of all of the inventory that would come along with the business. 
Please pull together a list of each inventory item you have stocked, including the store_id number, 
the inventory_id, the name of the film, the film’s rating, its rental rate and replacement cost. 
*/

-- Q2.
      
 SELECT 
            I.store_id,
            I.inventory_id,
            F.title,
            F.rating,
            F.rental_rate,
            F.replacement_cost
      FROM inventory AS I
      LEFT JOIN  film AS F
                    ON F.film_id = I.film_id
	 ORDER BY inventory_id ;

/* 
3.	From the same list of films you just pulled, please roll that data up and provide a summary level overview 
of your inventory. We would like to know how many inventory items you have with each rating at each store. 
*/

-- Q3.

SELECT 
    I.store_id,
    F.rating,
    COUNT(I.inventory_id) AS No_of_films
FROM inventory AS I
      LEFT JOIN  film AS F
                    ON F.film_id = I.film_id
GROUP BY I.store_id, F.rating;


/* 
4. Similarly, we want to understand how diversified the inventory is in terms of replacement cost. We want to 
see how big of a hit it would be if a certain category of film became unpopular at a certain store.
We would like to see the number of films, as well as the average replacement cost, and total replacement cost, 
sliced by store and film category. 
*/ 


-- Q4.

SELECT
       I.store_id,
       C.name,
      COUNT(F.film_id) AS NO_OF_FILMS,
      AVG(replacement_cost) AS AVG_REP_COST,
    SUM(F.replacement_cost )AS TOTAL_REP_COST
      
FROM inventory AS I
 LEFT JOIN film AS F
    ON F.film_id = I.film_id
    LEFT JOIN film_category AS FA
    ON I.film_id = FA.film_id
    LEFT JOIN category AS C
    ON C.category_id = FA.category_id
   LEFT JOIN store AS S
    ON S.store_id = I.store_id
    GROUP BY  C.name,I.store_id
    ORDER BY TOTAL_REP_COST desc;


/*
5.	We want to make sure you folks have a good handle on who your customers are. Please provide a list 
of all customer names, which store they go to, whether or not they are currently active, 
and their full addresses – street address, city, and country. 
*/

-- Q5.

SELECT 
      C.first_name,
      C.last_name,
      C.store_id,
      C.active,
      A.address,
      A.district,
      CI.city,
      CO.country
FROM customer AS C
LEFT JOIN address AS A
ON A.address_id = C.address_id
LEFT JOIN city AS CI
ON CI.city_id = A.city_id
LEFT JOIN country AS CO
ON CO.country_id = CI.country_id;

/*
6.	We would like to understand how much your customers are spending with you, and also to know 
who your most valuable customers are. Please pull together a list of customer names, their total 
lifetime rentals, and the sum of all payments you have collected from them. It would be great to 
see this ordered on total lifetime value, with the most valuable customers at the top of the list. 
*/

-- Q6.

SELECT 
     CU.first_name,
     CU.last_name,
     COUNT(P.rental_id) AS LIFE_TIME_RENTALS,
     SUM(P.amount) AS TOTAL_PAYMENTS
FROM customer AS CU 
LEFT JOIN rental AS R
ON CU.customer_id = R.customer_id
LEFT JOIN payment AS P
ON P.rental_id = R.rental_id
GROUP BY CU.first_name,
     CU.last_name
ORDER BY TOTAL_PAYMENTS DESC;
    
/*
7. My partner and I would like to get to know your board of advisors and any current investors.
Could you please provide a list of advisor and investor names in one table? 
Could you please note whether they are an investor or an advisor, and for the investors, 
it would be good to include which company they work with. 
*/

-- Q7.
SELECT 
     'advisor' AS ROLE,
     A.First_name ,
     A.last_name AS L_NAME,
     NULL
FROM advisor AS A
UNION
SELECT 
     'investor' AS ROLE,
     I.First_name ,
     I.last_name AS L_NAME,
     company_name AS COM_NAME
FROM investor AS I;

/*
8. We're interested in how well you have covered the most-awarded actors. 
Of all the actors with three types of awards, for what % of them do we carry a film?
And how about for actors with two types of awards? Same questions. 
Finally, how about actors with just one award? 
*/
-- Q8

SELECT 
 CASE WHEN actor_award.awards LIKE 'Emmy, Oscar, Tony ' THEN '3'
      WHEN actor_award.awards IN( 'Emmy, Oscar','Emmy, Tony','Oscar, Tony') THEN '2 '
     WHEN actor_award.awards IN('Emmy','Oscar','Tony') THEN ' 1' END AS NO_OF_AWARDS,
   -- AVG(CASE WHEN actor_id IS NULL THEN 0 ELSE 1 END  ) AS PCT_W_ONE_FLIM
AVG(actor_id)/100
FROM actor_award
GROUP BY NO_OF_AWARDS;
