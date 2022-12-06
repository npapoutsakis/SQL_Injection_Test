gcc (Ubuntu 11.3.0-1ubuntu1~22.04) 11.3.0

#            Systems and Security PLH519
#           
#       		Topic: SQL Injection
#
#      Author: Nikolaos Papoutsakis 2019030206


In this assignment we tested an app that is vurnerable to the SQL injection attack.

At first, we had to bypass the login page using an SQL query that 'breaks' the SQL 
command and login as user. To achieve that, we had to see the query that the app was
running in order to check the credentials and login.

The SQL query was:
	
	SELECT * FROM users WHERE username = 'user' AND password = '{password}' ,

and had to import only the password of the user.

To break the SQL command, we just terminated the condition ... AND password = '{password}'
by adding the following code in the password section:

	' or true--

With the specific input the command takes the form:
	
	SELECT * FROM users WHERE username = 'user' AND password = '' or true--'

The script adds a semicolon and the password input remains empty. Now, the condition
AND password = '' will fail because the password must exist so now the program runs 
the next condition:  OR true and makes a successful login. The -- was added to comment
the last semicolon at the end so that we have no syntax error. 


Now that we successfully logged in as user, we could search for an item that the user had.
Our goal was to get the credentials of the superadmin and login. After checking the app.py
we noticed that the SQL query for searching was:

	SELECT name,category,price FROM items WHERE name = '{name}' ,

and had to import only the name of the item.

We noticed the information that the tables had. Both of them had the primary key and some info.
So we imported the specific script:

	' UNION SELECT * FROM users--

With the specific input the command takes the form:
	
	SELECT name,category,price FROM items WHERE name = '' UNION SELECT * FROM users--'
	
Same as before, the script adds a semicolon and the name input remains empty. The query now
will fail because the table contains items that have a name. The UNION operator will return 
a table that contains both the content of the queries:

	1. SELECT name,category,price FROM items WHERE name = ''
	2. SELECT * FROM users

The 1st query will fail, as we said, and now the return value will be the content of the uses
table.

As a result, the app will display the information of the user table that contains the username,
password and ID(primary key) of the superadmin. The results we got are:

The password of the superadmin:
	superadmin

The output flag:
	TUC{fakeflagfortesting}

