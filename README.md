# DB-MIGRATION
CREATE TABLE IF NOT EXISTS temp (
	temp_id serial PRIMARY KEY,
	temp_array VARCHAR[]
);

//in here I create a temporary table where I can do my changes easily and then migrate to my main db. I make temp_id serial to make it ordered(1,2,3). I create array as we will later need array to store our data.//

INSERT INTO temp (temp_id)
SELECT student_id FROM interests ON CONFLICT DO NOTHING;
//As we know from the HW document, we need to sort interests in order to ID number. Therefore we insert students_id from interests to temp (temp_id). ON CONFLICT DO NOTHING will help us to sort IDs like if second ID 1 will come it will deop it automatically.( wont take it). So that every ID will be taken once.//


UPDATE temp
	SET temp_array = ARRAY(SELECT interest FROM interests WHERE student_id = temp_id);
  
 //we will change data on temp table, SELECT will select hobbies that has same ID into one query.SET temp array will later set this hobby queries into array.

TRUNCATE interests;
// we cleas all the data from interest table

ALTER TABLE interests
	ALTER COLUMN interest TYPE VARCHAR[] USING ARRAY[interest];
ALTER TABLE interests
	RENAME COLUMN interest TO interests;
  // change string to string array and rename column name from interest to interests//

INSERT INTO interests
SELECT * FROM temp;
// As the structure of temp array is same with student array it is now easy to copy all the data from temp array.

ALTER TABLE students
	RENAME COLUMN st_id TO student_id;
ALTER TABLE students
	ALTER COLUMN st_name TYPE VARCHAR(30),
	ALTER COLUMN st_last TYPE VARCHAR(30);
  // it is changinf column name and size of string

DROP TABLE temp;
