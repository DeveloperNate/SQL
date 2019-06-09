# SQL
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<h1> SQL Tasks </h1>\n",
    "<h3> PIVOT </h3>\n",
    "<img src = \"Pivot.JPG\">"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "SELECT Doctor, Professor, Singer, Actor  FROM \n",
    "(SELECT name, occupation, ROW_NUMBER() OVER (PARTITION BY occupation ORDER BY name)as rn FROM OCCUPATIONS )\n",
    "PIVOT\n",
    "( MAX(name) FOR occupation  IN ('Doctor' as Doctor ,'Professor' as Professor ,'Singer' as Singer ,'Actor' as Actor)) \n",
    "ORDER BY rn ;"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This SQL Select Query is divided into three sections. \n",
    "<ul> \n",
    "    <li> The first SELECT Query selects the columns from the new pivot table that was created</li>\n",
    "    <li> The second SELECT Query creates a new Table from the OCCUAPTION Table. This table contains the name and occupation as well as the row number. The rows will be counted based on the occupation  field.</li>\n",
    "    <li> The last section pivots the table. This has aggregated the name field despite not being needed. Additionally, it has created the columns that have been called in the first SELECT Query.</li>\n",
    "    </ul>\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<h3> CASE </h3>\n",
    "<img src=\"Case.JPG\">"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "SELECT N,\n",
    "CASE \n",
    "    WHEN P IS NULL THEN 'Root'\n",
    "    WHEN N IN (SELECT P FROM BST) THEN 'Inner'\n",
    "    ELSE 'Leaf'\n",
    "END\n",
    "FROM BST\n",
    "ORDER BY N ASC;"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Using the CASE function we can add a value into the result depending on a value that was retrieved from the SELECT Query. In the above answer, the CASE statement looks at the value in P and if that value is NULL, Root will be outputted. If the value of N is in the P table, it outputs Inner. Otherwise, it will output Leaf."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<h3> Using POWER/ SQRT for Math Equations</h3>"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<img src=\"MATHS.JPG\">"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "SELECT ROUND(SQRT(POWER((a - b),2) + POWER((c - d),2)),4) FROM \n",
    "(SELECT MIN(LAT_N) AS a,MAX(LAT_N) AS b ,MIN(LONG_W) AS c ,MAX(LONG_W) AS d FROM STATION);"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The first SELECT Query uses the aggregated values from the results of the second query to calculate the Euclidean Distance. "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<h3> Join with CASE  </h3>\n",
    "<img src = \"join.JPG\">"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "SELECT \n",
    "CASE \n",
    "    WHEN grade < 8 THEN 'NULL'\n",
    "    ELSE name\n",
    "END AS name\n",
    ",GRADES.grade,STUDENTS.marks\n",
    "FROM STUDENTS\n",
    "JOIN GRADES ON STUDENTS.marks BETWEEN GRADES.min_mark AND GRADES.max_mark\n",
    "ORDER BY GRADES.grade DESC , name ASC , STUDENTS.marks ASC; "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This Query is divided up into two parts\n",
    "<ul>\n",
    "    <li> Case - This checks if the grade is lower then 8 and return NULL or the NAME of the student depending on the answer</li>\n",
    "    <li> Join - This does a join action depending on whether the student's mark on the grade boundaries. The correct grade value is then added onto the table.  "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<h3> Join with Having </h3>\n",
    "<img src =\"join_with_having.JPG\">"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "SELECT HACKERS.hacker_id, HACKERS.name \n",
    "FROM HACKERS \n",
    "INNER JOIN SUBMISSIONS ON HACKERS.hacker_id = SUBMISSIONS.hacker_id\n",
    "INNER JOIN DIFFICULTY ON SUBMISSIONS.score = DIFFICULTY.score \n",
    "INNER JOIN CHALLENGES ON SUBMISSIONS.challenge_id = CHALLENGES.challenge_id AND DIFFICULTY.difficulty_level = CHALLENGES.difficulty_level\n",
    "GROUP BY HACKERS.hacker_id, HACKERS.name HAVING COUNT(DISTINCT SUBMISSIONS.submission_id )> 1\n",
    "ORDER BY COUNT(DISTINCT SUBMISSIONS.submission_id ) DESC, hacker_id ASC;"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The Query above selects the hacker_id and name of the hackers that have a full score in more than one challenge. This is done by joining the 4 tables together. However, the second join only selects the records that have the max score, so removing any records that are not full marks. The HAVING condition in the group by creates a condition for the grouping, so removing the record of any hacker that did not have full marks in more than one submission. \n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<h3> Join with OVER </h3> \n",
    "<img src=\"join with over.JPG\">"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "SELECT id , age , min_coins,power FROM\n",
    "(SELECT WANDS.id, WANDS_PROPERTY.age, WANDS.coins_needed, WANDS.power , MIN(WANDS.coins_needed) OVER (PARTITION BY WANDS_PROPERTY.code,WANDS_PROPERTY.age,WANDS.power) AS min_coins \n",
    "FROM WANDS \n",
    "INNER JOIN WANDS_PROPERTY ON WANDS.code = WANDS_PROPERTY.code \n",
    "WHERE WANDS_PROPERTY.is_evil = 0) \n",
    "WHERE coins_needed = min_coins \n",
    "ORDER BY power DESC, age DESC;"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This SELECT Query uses a subquery with the over method to add the min coins needed for the wands with the same age and power. The main Query was then able to extract the essential information needed and place it in the required order. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.6.6"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
