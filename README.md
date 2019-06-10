<h1>SQL Examples</h1>

<h2>PIVOT </h2>

<h3>Question</h3>
<img src = "Pivot.JPG"> 

<h3>Answer</h3>
      SELECT Doctor, Professor, Singer, Actor  
      FROM 
      (SELECT name, occupation, ROW_NUMBER() OVER (PARTITION BY occupation ORDER BY name)as rn FROM OCCUPATIONS )
      PIVOT
      ( MAX(name) FOR occupation  IN ('Doctor' as Doctor ,'Professor' as Professor ,'Singer' as Singer ,'Actor' as Actor)) 
      ORDER BY rn ;


This SQL Select Query is divided into three sections.

  1) The first SELECT Query selects the columns from the new pivot table that was created
  2) The second SELECT Query creates a new Table from the OCCUAPTION Table. This table contains the name and occupation as well as the row number. The rows will be counted based on the occupation field.
  3) The last section pivots the table. This has aggregated the name field despite not being needed. Additionally, it has created the columns that have been called in the first SELECT Query.

