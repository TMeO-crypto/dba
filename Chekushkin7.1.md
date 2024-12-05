sudo -u postgres psql
[sudo] пароль для tmeo: 
psql (16.4 (Ubuntu 16.4-0ubuntu0.24.04.2))
Type "help" for help.

postgres=# \c db1
You are now connected to database "db1" as user "postgres".
db1=# SELECT * FROM t;
ERROR:  relation "t" does not exist
LINE 1: SELECT * FROM t;
                      ^
db1=# CREATE TABLE t(
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    s text
);
CREATE TABLE
db1=# INSERT INTO t(s) VALUES ('Привет, мир!'), (''), (NULL);
INSERT 0 3
db1=# SELECT * FROM t;
 id |      s       
----+--------------
  1 | Привет, мир!
  2 | 
  3 | 
(3 rows)

db1=# COPY t TO stdout;
1	Привет, мир!
2	
3	\N
db1=# COPY t TO stdout WITH (NULL '<NULL>', DELIMITER ',');
1,Привет\, мир!
2,
3,<NULL>
db1=# COPY (SELECT * FROM t WHERE s IS NOT NULL) TO stdout;
1	Привет, мир!
2	
db1=# COPY t TO stdout WITH (FORMAT csv);
1,"Привет, мир!"
2,""
3,
db1=# TRUNCATE TABLE t;
TRUNCATE TABLE
db1=# COPY t FROM stdin WHERE id != 2;
Enter data to be copied followed by a newline.
End with a backslash and a period on a line by itself, or an EOF signal.
>> 1	Привет, мир!
2	
3	\N
\.>> >> >> 
COPY 2
db1=# \pset null '\\N'
Null display is "\N".
db1=# SELECT * FROM t;
 id |      s       
----+--------------
  1 | Привет, мир!
  3 | \N
(2 rows)

db1=# TRUNCATE TABLE t;
TRUNCATE TABLE
db1=# COPY t FROM stdin;
Enter data to be copied followed by a newline.
End with a backslash and a period on a line by itself, or an EOF signal.
>> 1	Привет, мир!
2	
3	\N
\.>> >> >> 
COPY 3
db1=# SELECT * FROM t;
 id |      s       
----+--------------
  1 | Привет, мир!
  2 | 
  3 | \N
(3 rows)

