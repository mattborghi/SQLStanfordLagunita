 Q1
(1 point possible)
Find the names of all students who are friends with someone named Gabriel. 

SELECT name FROM (SELECT ID2 from Highschooler JOIN Friend ON Highschooler.ID = Friend.ID1 WHERE name = 'Gabriel' ORDER BY name) AS F JOIN Highschooler ON F.ID2 = Highschooler.ID;

Your Query Result:
Alexis
Andrew
Cassandra
Jessica
Jordan

 Q2
(1 point possible)
For every student who likes someone 2 or more grades younger than themselves, return that student's name and grade, and the name and grade of the student they like. 

SELECT H1.name, H1.grade, H2.name, H2.grade 
FROM Highschooler as H1
JOIN Likes ON H1.ID = Likes.ID1 
JOIN Highschooler H2 ON H2.ID = Likes.ID2
WHERE H2.grade <= H1.grade - 2;

Your Query Result:
John	12	Haley	10

 Q3
(1 point possible)
For every pair of students who both like each other, return the name and grade of both students. Include each pair only once, with the two names in alphabetical order. 

SELECT h1.name, h1.grade, h2.name, h2.grade from Likes l1, Likes l2, Highschooler h1, Highschooler h2
WHERE l1.ID1=l2.ID2 and l2.ID1=l1.ID2 and l1.ID1=h1.ID and l1.ID2=h2.ID AND h1.name < h2.name;

Your Query Result:
Cassandra	9	Gabriel	9
Jessica	11	Kyle	12


 Q4
(1 point possible)
Find all students who do not appear in the Likes table (as a student who likes or is liked) and return their names and grades. Sort by grade, then by name within each grade.

SELECT H3.name,H3.grade FROM Highschooler AS H3 WHERE NOT EXISTS (SELECT H1.name as N1,H2.name as N2 FROM Highschooler AS H1 JOIN Likes AS L1 ON H1.ID = L1.ID1 JOIN Highschooler AS H2 ON L1.ID2=H2.ID WHERE H3.ID = H1.ID or H3.ID = H2.ID) ORDER BY H3.grade,H3.name;

Your Query Result:
Jordan	9
Tiffany	9
Logan	12

 Q5
(1 point possible)
For every situation where student A likes student B, but we have no information about whom B likes (that is, B does not appear as an ID1 in the Likes table), return A and B's names and grades.

SELECT H.name,H.grade,H2.name,H2.grade 
FROM Highschooler AS H 
JOIN (SELECT M.ID1,T.ID2 FROM Likes as M, 
(SELECT DISTINCT ID2 FROM Likes WHERE ID2 NOT IN (SELECT ID1 FROM Likes)) AS T WHERE M.ID2 = T.ID2) AS N ON H.ID = N.ID1 
JOIN Highschooler AS H2 ON H2.ID = N.ID2;

Your Query Result:
Alexis	11	Kris	10
Austin	11	Jordan	12
Brittany	10	Kris	10
John	12	Haley	10

Q6
(1 point possible)
Find names and grades of students who only have friends in the same grade. Return the result sorted by grade, then by name within each grade. 

SELECT  H1.name, H1.grade
FROM Highschooler AS H1
JOIN Friend AS F ON F.ID1=H1.ID
JOIN Highschooler AS H2 ON H2.ID = F.ID2
WHERE H1.grade=H2.grade
AND NOT EXISTS (SELECT ID FROM Highschooler WHERE ID IN (
  SELECT ID2 FROM Friend WHERE ID1=H1.ID) AND grade <> H1.grade)
GROUP BY H1.name
ORDER BY H1.grade, H1.name

Your Query Result:
Jordan	9
Brittany	10
Haley	10
Kris	10
Gabriel	11
John	12
Logan	12

Q7
(1 point possible)
For each student A who likes a student B where the two are not friends, find if they have a friend C in common (who can introduce them!). For all such trios, return the name and grade of A, B, and C. 

select h1.name, h1.grade, h2.name, h2.grade, h3.name, h3.grade
from Highschooler h1, Highschooler h2, Highschooler h3,
(
select l.id1 as lid1, l.id2 as lid2, f2.id1 as f2id1 from Likes l, Friend f2, Friend f3 where
not exists (select f.id1, f.id2 from Friend f where f.id1 = l.id1 and f.id2 = l.id2)
and f2.id2 = l.id1 and f3.id2 = l.id2 and f2.id1 = f3.id1
) as t
where h1.id = t.lid1 and h2.id = t.lid2 and h3.id = f2id1;

Your Query Result:
Andrew	10	Cassandra	9	Gabriel	9
Austin	11	Jordan	12	Andrew	10
Austin	11	Jordan	12	Kyle	12

Q8
(1 point possible)
Find the difference between the number of students in the school and the number of different first names. 

SELECT count(name)-count(DISTINCT name) AS Difference FROM Highschooler ORDER BY name;

Your Query Result:
2

Q9
(1 point possible)
Find the name and grade of all students who are liked by more than one other student. 

SELECT M.name,M.grade 
FROM (SELECT L.ID1,H.name,H.grade FROM Highschooler AS H
JOIN Likes AS L ON L.ID2 = H.ID ORDER BY H.name) AS M
GROUP BY M.name,M.grade 
HAVING count(*) > 1;

Your Query Result:
Cassandra	9
Kris	10


### PART 2

Q1
(1 point possible)
It's time for the seniors to graduate. Remove all 12th graders from Highschooler. 

DELETE FROM Highschooler
WHERE grade = 12;

To check your data modification statement, we ran the following query after your modification: select * from Highschooler order by ID

Your Query Result:
1101	Haley	10
1247	Alexis	11
1316	Austin	11
1381	Tiffany	9
1468	Kris	10
1501	Jessica	11
1510	Jordan	9
1641	Brittany	10
1689	Gabriel	9
1709	Cassandra	9
1782	Andrew	10
1911	Gabriel	11

Q2
(1 point possible)
If two students A and B are friends, and A likes B but not vice-versa, remove the Likes tuple. 

DELETE from Likes
WHERE ID2 IN (SELECT ID2 FROM Friend WHERE Likes.ID1 = ID1) and
      ID2 NOT IN (SELECT L.ID1 FROM Likes L WHERE Likes.ID1 = L.ID2);

To check your data modification statement, we ran the following query after your modification: select H1.name, H1.grade, H2.name, H2.grade from Likes L, Highschooler H1, Highschooler H2 where L.ID1 = H1.ID and L.ID2 = H2.ID order by H1.name, H1.grade

Your Query Result:
Alexis	11	Kris	10
Andrew	10	Cassandra	9
Austin	11	Jordan	12
Cassandra	9	Gabriel	9
Gabriel	9	Cassandra	9
Jessica	11	Kyle	12
John	12	Haley	10
Kyle	12	Jessica	11

Q3
(1 point possible)
For all cases where A is friends with B, and B is friends with C, add a new friendship for the pair A and C. Do not add duplicate friendships, friendships that already exist, or friendships with oneself. (This one is a bit challenging; congratulations if you get it right.) 

INSERT INTO Friend 
SELECT F1.iD1, F2.iD2 FROM Friend F1, Friend F2
WHERE F1.iD2 = F2.iD1
AND F1.iD1 <> F2.iD2
EXCEPT 
SELECT * FROM Friend;

To check your data modification statement, we ran the following query after your modification: select ID, name, grade, (select count(*) from Friend where id1 = H.id) from Highschooler H order by ID

Your Query Result:
1025	John	12	2
1101	Haley	10	3
1247	Alexis	11	7
1304	Jordan	12	8
1316	Austin	11	6
1381	Tiffany	9	6
1468	Kris	10	6
1501	Jessica	11	7
1510	Jordan	9	5
1641	Brittany	10	3
1661	Logan	12	4
1689	Gabriel	9	8
1709	Cassandra	9	7
1782	Andrew	10	10
1911	Gabriel	11	5
1934	Kyle	12	7
