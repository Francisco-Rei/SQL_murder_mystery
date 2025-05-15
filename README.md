# SQL_murder_mystery
This is a challenge from [Summer of SQL](https://github.com/wjsutton/the_summer_of_sql) to help gain skills and confidence in SQL.
Skills tested in challenge 1:
 - Filtering with WHERE
 - JOINing tables
 - Aggregations and GROUP BY
 - String Functions and Pattern Matching (LIKE)

Challenge: SQL Murder Mystery.
A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. You vaguely remember that the crime was a ​murder​ that occurred sometime on ​Jan.15, 2018​ and that it took place in ​SQL City​. Start by retrieving the corresponding crime scene report from the police department’s database.

## Database
![Mystery Project Schema](https://mystery.knightlab.com/schema.png)


### Query 1
Retrieve the crime scene report from the police department's database
````sql
SELECT *
FROM crime_scene_report
WHERE date = '20180115' AND city = 'SQL City' AND type = 'murder';
````
| Date     | Type   | Description  | City     |
|----------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| 20180115 | Murder | Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave". | SQL City |

### Query 2
Find the 2 witnesses
 - Witness 1 lives at the last house on "Northwestern Dr"
````sql
SELECT *
FROM person
WHERE address_street_name = 'Northwestern Dr'
ORDER BY address_number DESC
LIMIT 1;
````
| id   | name           | license_id | address_number | address_street_name | ssn       |
|------|----------------|------------|----------------|----------------------|-----------|
| 14887 | Morty Schapiro | 118009     | 4919           | Northwestern Dr       | 111564949 |

- Witness 2 is named Annabel, lives somewhere on "Franklin Ave"
````sql
SELECT *
FROM person
WHERE address_street_name = 'Franklin Ave' AND name LIKE '%Annabel%'
````
| id    | name            | license_id | address_number | address_street_name | ssn       |
|-------|------------------|------------|----------------|----------------------|-----------|
| 16371 | Annabel Miller   | 490173     | 103            | Franklin Ave          | 318771143 |

### Query 3
Find out what the witnesses with ID 14887 and 16371 said in the interviews
````sql
SELECT *
FROM interview
WHERE person_id = 14887 OR person_id = 16371
````
| person_id | transcript |
|-----------|------------|
| 14887 | I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W". |
| 16371 | I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th. |

### Query 4
What we know about the killer from the witnesses' testimony:
- The killer had a "Get Fit Now Gym" bag. Only gold members have these bags. 
- The membership number on the bag started with "48Z". 
- The man got into a car with a plate that included "H42W".
- The killer was working out last week on January the 9th.
````sql
SELECT p.*
FROM drivers_license AS dl
INNER JOIN person AS p ON dl.id = p.license_id
INNER JOIN get_fit_now_member AS gf ON p.id = gf.person_id
WHERE plate_number LIKE '%H42W%' AND membership_status = 'gold'
````
| id     | name          | license_id | address_number | address_street_name        | ssn       |
|--------|---------------|------------|----------------|-----------------------------|-----------|
| 67318  | Jeremy Bowers | 423327     | 530            | Washington Pl, Apt 3A       | 871539279 |

### Query 5
````sql
Find out what the killer said in the interview
SELECT *
FROM interview
WHERE person_id = 67318
````
| person_id | transcript |
|-----------|------------|
| 67318     | I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017. |

 ### Query 6
The killer was hired by a woman. Her description:
- Height: around 5'5" (65") or 5'7" (67").
- Hair: Red
- Car: Tesla Model S.
- Other: Attended the SQL Symphony Concert 3 times in December 2017.
````sql
SELECT p.*, COUNT(fb.person_id) AS apperances_at_SQL_symphony
FROM drivers_license AS dl
INNER JOIN person AS p ON p.license_id = dl.id
INNER JOIN facebook_event_checkin AS fb ON fb.person_id = p.id
WHERE gender= 'female' AND hair_color= 'red' AND car_make = 'Tesla' AND car_model = 'Model S' AND height >= 65 AND height <= 67
GROUP BY name
````
| id     | name             | license_id | address_number | address_street_name | ssn       | appearances_at_SQL_symphony |
|--------|------------------|------------|----------------|----------------------|-----------|------------------------------|
| 99716  | Miranda Priestly | 202298     | 1883           | Golden Ave           | 987756388 | 3                            |

## Checking Solution
````sql
INSERT INTO solution VALUES (1, 'Miranda Priestly');
SELECT value FROM solution;
````
| value |
|-------|
| Congrats, you found the brains behind the murder! Everyone in SQL City hails you as the greatest SQL detective of all time. Time to break out the champagne! |



