 Q1
(1/1 point)
Find the titles of all movies directed by Steven Spielberg.

SELECT title FROM Movie WHERE director = 'Steven Spielberg';

Your Query Result:
E.T.
Raiders of the Lost Ark


 Q2
(1/1 point)
Find all years that have a movie that received a rating of 4 or 5, and sort them in increasing order.

SELECT DISTINCT year FROM Movie INNER JOIN Rating ON Rating.mID = Movie.mID WHERE stars >= 4 ORDER BY year asc;

Your Query Result:
1937
1939
1981
2009

 Q3
(1/1 point)
Find the titles of all movies that have no ratings.

SELECT title FROM Movie WHERE mID NOT IN (SELECT mID FROM Rating);

Your Query Result:
Star Wars
Titanic

 Q4
(1/1 point)
Some reviewers didn't provide a date with their rating. Find the names of all reviewers who have ratings with a NULL value for the date. 

SELECT name FROM Rating INNER JOIN Reviewer ON Rating.rID = Reviewer.rID WHERE ratingDate is null;

Your Query Result:
Chris Jackson
Daniel Lewis

 Q5
(1/1 point)
Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars.


SELECT name,title,stars,ratingDate FROM Rating INNER JOIN Movie ON Rating.mID = Movie.mID INNER JOIN Reviewer ON Rating.rID = Reviewer.rID ORDER BY name,title,stars asc;

Your Query Result:
Ashley White	E.T.	3	2011-01-02
Brittany Harris	Raiders of the Lost Ark	2	2011-01-30
Brittany Harris	Raiders of the Lost Ark	4	2011-01-12
Brittany Harris	The Sound of Music	2	2011-01-20
Chris Jackson	E.T.	2	2011-01-22
Chris Jackson	Raiders of the Lost Ark	4	<NULL>
Chris Jackson	The Sound of Music	3	2011-01-27
Daniel Lewis	Snow White	4	<NULL>
Elizabeth Thomas	Avatar	3	2011-01-15
Elizabeth Thomas	Snow White	5	2011-01-19
James Cameron	Avatar	5	2011-01-20
Mike Anderson	Gone with the Wind	3	2011-01-09
Sarah Martinez	Gone with the Wind	2	2011-01-22
Sarah Martinez	Gone with the Wind	4	2011-01-27

 Q6
(1/1 point)
For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie. 

select Reviewer.name, Movie.title
from Reviewer, Movie, (select R1.rID, R1.mID from Rating R1, Rating R2 where R1.rID=R2.rID and R1.mID=R2.mID and R2.ratingDate>R1.ratingDate and R2.stars>R1.stars) as T
where Reviewer.rID=T.rID and Movie.mID=T.mID

Your Query Result:
Sarah Martinez	Gone with the Wind

 Q7
(1/1 point)
For each movie that has at least one rating, find the highest number of stars that movie received. Return the movie title and number of stars. Sort by movie title.

SELECT title, max(stars) FROM Movie JOIN Rating ON Movie.mID = Rating.mID GROUP BY title;


Your Query Result:
Avatar	5
E.T.	3
Gone with the Wind	4
Raiders of the Lost Ark	4
Snow White	5
The Sound of Music	3

 Q8
(1/1 point)
For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie title.

SELECT title, max(stars)-min(stars) as Spread FROM Movie JOIN Rating ON Movie.mID = Rating.mID GROUP BY title ORDER BY Spread desc, title;

Your Query Result:
Avatar	2
Gone with the Wind	2
Raiders of the Lost Ark	2
E.T.	1
Snow White	1
The Sound of Music	1

 Q9
(1/1 point)
Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.) 

SELECT avg(T.Bifore) - avg(S.After) AS Difference FROM (SELECT title, avg(stars) as Bifore FROM Movie JOIN Rating ON Movie.mID = Rating.mID WHERE year < 1980 GROUP BY title) AS T, (SELECT M2.title,avg(R2.stars) as After FROM Movie M2 JOIN Rating R2 ON M2.mID = R2.mID WHERE M2.year > 1980 GROUP BY M2.title) AS S;

Your Query Result:
0.0555555555556

# PART 2

Q1
(1 point possible)
Add the reviewer Roger Ebert to your database, with an rID of 209. 

INSERT INTO Reviewer 
values (209, 'Roger Ebert');

To check your data modification statement, we ran the following query after your modification: select * from Reviewer order by rID, name

Your Query Result:
201	Sarah Martinez
202	Daniel Lewis
203	Brittany Harris
204	Mike Anderson
205	Chris Jackson
206	Elizabeth Thomas
207	James Cameron
208	Ashley White
209	Roger Ebert

Q2
(1 point possible)
Insert 5-star ratings by James Cameron for all movies in the database. Leave the review date as NULL. 

INSERT INTO Rating (rID,mID,stars) 
SELECT 207,M.mID,5 FROM Movie AS M;

Correct

To check your data modification statement, we ran the following query after your modification: select * from Rating where stars = 5 order by rID, mID

Your Query Result:
206	106	5	2011-01-19
207	101	5	<NULL>
207	102	5	<NULL>
207	103	5	<NULL>
207	104	5	<NULL>
207	105	5	<NULL>
207	106	5	<NULL>
207	107	5	<NULL>
207	107	5	2011-01-20
207	108	5	<NULL>

Q3
(1 point possible)
For all movies that have an average rating of 4 stars or higher, add 25 to the release year. (Update the existing tuples; don't insert new tuples.) 

UPDATE Movie
SET year = year + 25
WHERE mID IN (SELECT M.mID as title FROM Movie AS M JOIN Rating AS R ON R.mID = M.mID GROUP BY title,year HAVING avg(stars) >= 4);

To check your data modification statement, we ran the following query after your modification: select * from Movie order by mID

Your Query Result:
101	Gone with the Wind	1939	Victor Fleming
102	Star Wars	1977	George Lucas
103	The Sound of Music	1965	Robert Wise
104	E.T.	1982	Steven Spielberg
105	Titanic	1997	James Cameron
106	Snow White	1962	<NULL>
107	Avatar	2034	James Cameron
108	Raiders of the Lost Ark	1981	Steven Spielberg

Q4
(1 point possible)
Remove all ratings where the movie's year is before 1970 or after 2000, and the rating is fewer than 4 stars. 

DELETE FROM Rating
WHERE mID IN (

SELECT M.mID FROM Movie AS M  
WHERE (year 
NOT BETWEEN 1970 AND 2000)
)
AND ( stars < 4 );

Correct

To check your data modification statement, we ran the following query after your modification: select R.rID, R.mID, R.stars, M.title, M.year from Rating R join Movie M on (R.mID = M.mID) order by R.rID, R.mID

Your Query Result:
201	101	4	Gone with the Wind	1939
202	106	4	Snow White	1937
203	108	2	Raiders of the Lost Ark	1981
203	108	4	Raiders of the Lost Ark	1981
205	104	2	E.T.	1982
205	108	4	Raiders of the Lost Ark	1981
206	106	5	Snow White	1937
207	107	5	Avatar	2009
208	104	3	E.T.	1982
