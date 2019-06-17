# Sthack 2019 - CTF - Ticket Checker
*Creator of the challenge:* Ghozt

*Category:* WEB

*Difficulty:* Medium

## **Step 1: Follow the errors**

Reaching the website, we get a webpage allowing you to scan a ticket (QRCode) using your camera.

Once done, we get the following error:

![error_json](/error_json.png)

Hum...

From Burp, we intercept the request and save it for later.

We use the tool "qrencode" to create a QRCode to put the content I want inside.

For all the next steps, the process will be like the following:
* Create a QRCode using qrencode, containing our data to be tested inside
* Base64 encoded the output of the command
* Send it throught the repeater

Let's start with an empty json:

![json_empty](/qrencode_base.png)
+  *qrencode '{}' -o - | base64 -w 0*
+  *-o -*: output directly on the standard output
+  *-w 0* : disable line wrapping

We put the output into the raw-data of the data image in the "**comment**" parameter.

Be carefull to encode the base64 just added to avoid the following error:
![error_2_encoding](/error_2_encoding.png)

Once encoded:

![parameter](/json_empty.png)

OK ! Missing the key '<b>uid</b>' in the JSON.

![json_uid](/json_uid.png)

## **Step 2: Find the entry point**

Let's add it...

![qrencode_uid](/qrencode_uid.png)

Now we get a correct output telling us that the ticket has already been used:

![output_uid](/output_uid.png)

But if we use one which may be unknown, we get a database error.

![error_db](/error_db.png)

## **Step 3: Injection SQL - Integer Based**

Let's try for some Injection SQL as we are talking to the database!

I will pass throught all SQL Injection tested as one using 'single quote', "double quote" and so on... Here, it is an interger based!

We need to know the right number used by the database. Using "order by" with 5 columns, we get an error: 

![order_by](/order.png)

But using 4, it is ok meaning that we have 4 columns used. Output "2" and "3" are displayed, we can use it to exploit our SQL Injection:

![test_1_2_3_4](/union_1_2_3_4.png)

![reflect_value](/reflected_value.png)

Finding the current user, the database... 

![output_sqli](/output_sqli.png)

**Nice** but I can not do anything better with that? Actually yes, let's try to read some local files!

![load_file](/load_file.png)

![check](/check.png)

**Great!!!**
We can leak the source code!

The usefull files to continue the challenge are:
* check.php
* ticket.php

## **Step 4: Read the source code**

By analyzing the check.php and the Ticket.php files, our goal is to reach the call to the log() function from the Ticket class.

So let make the program do it.

If there is no **t_uid, object, and sign** keys in the JSON, the program terminates is execution.

![die4](/die4.png)

We add those keys to continue the execution and pass the condition.

The program get a KEY got from the database which will be used to create a Signature object.

Let's grab it! It would be usefull:

![key](/KEY.png)

![KEY_2](/KEY_2.png)

After getting the key, the program create a Signature Object. Call the method Check(), dies if the check is wrong.

We get the source code of the Ticket.php file on our machine to modify it and pass the check. We need to change some part:

* Changing the filename in the History class
* Hardcoding the key in the Signature class
* Adding the following source code to pass the right value of the **object** and **sign** keys

![code_modified](/code_modified.png)



The source code of the ticket.php file reveals a vulnerability: **A PHP Object Injection!**

But before to get it... take your car, and let's drive **a lot!**, because the final step is still far away! (Thank you so much GHOZT for that!)

## **Step 5: Create your most beautiful object to RCE!** //ID cmd

We know that ...

## **Step 6: Get the flag, and go drink a beer!** //RCE shell

ls /

cat /flag.txt

Flag is: FLAG

![Cheers](/verre-chope.jpg)
