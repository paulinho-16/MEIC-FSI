# SQL Injection Attack Lab

## Task 1

- Using the command `SHOW TABLES;` we were able to see that the only table of the database is called `credentials`.
- Then, we created a query to retrieve the information about the user called Alice, shown in the following print:

![Task 1 screenshot](images/Lab4Task1.png)

## Task 2

#### Task 2.1

- Knowing that the username of the administrator account is *admin*, we could ignore the password verification by ending the query with an *;* and commenting the rest of the line, using the symbol *#*. This way, we could use the field of the username input to do that, by entering the string `admin'; #`.
- Then, we successfully logged in as an administrator, and we can see the information of all the employees.

![Task 2.1 screenshot](images/Lab4Task2Step1.png)

#### Task 2.2

- To send an HTTP GET request in order to login as an administrator, we must first prepare its url.
- We did it in a way that the input of the username field is the same of the previous task and that the special characters are properly encoded. In this case, we needed to replace the *'* character by *%27* and the space characters by *%20*.
- The final command to run is `curl 'www.seed-server.com/unsafe_home.php?username=admin%27;%20--%20'`

![Task 2.2 screenshot](images/Lab4Task2Step2.png)

#### Task 2.3

