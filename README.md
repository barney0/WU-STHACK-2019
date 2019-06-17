# Sthack 2019 - CTF - Ticket Checker

<u>Creator of the challenge:</u> Ghozt
<u>Category:</u> WEB
<u>Difficulty:</u> Medium

## **Step 1: Follow the errors**

Reaching the website, we get a webpage allowing you to scan a ticket (QRCode) from your camera.

Once done, we get the following error: **"Error - Could not parse json"**.

SCREENSHOT ERROR BURP

Hum...

From Burp, we intercept the request and save it for later.

We use the tool "qrencode" to create a QRCode to put the content I want inside.

We start with an empty json:

SCREENSHOT QRENCODE CMD

+  *qrencode '{}' -o - | base64 -w 0*
+  *-o -*: output directly on the standard output
+  *-w 0* : disable line wrapping

We put the output into the raw-data of the data image in the "**comment**" parameter.

Be carefull of the encoding here!

SCREENSHOT COMMENT PARAM

OK ! Missing the key '<b>uid</b>' in the JSON.

## **Step 2: Find the entry point**

Let's add it...

SCREENSHOT QRENCODE CMD ID = 1

Now we get a correct output telling us that the ticket has already been used.

But... what if instead of the id 1, we use one maybe unknown?

SCREENSHOT QRENCODE CMD BIG ID

GREAT database error!

## **Step 3: Injection SQL - Integer Based**

Let's try for some Injection SQL!

I will pass throught all SQL Injection tested with single quote, double quote and so on... It is an interger one here!

Finding the right number of columns...

SCREENSHOT QRENCODE CMD

SCREENSHOT SQL INJECTION ORDER BY OUTPUT

Finding the current user, the database... 

SCREENSHOT QRENCODE CMD VERSION AND DB

SCREENSHOT CURRENT VERSION AND DATABASE OUTPUT

**Nice** but I can not do anything better with that? Actually yes, let's try to read some local files!

SCREENSHOT QRENCODE CMD LOAD_FILE

SCREENSHOT CURRENT LOAD_FILE /var/www/html/index.php OUTPUT

## **Great!!!**
Let's leak some code here!

We get the following source code:
* check.php
* ticket.php
* dbb.php

## **Step 4: Read the source code**

SCREENSHOT OF CHECK.PHP

SCREENSHOT OF TICKET.PHP

On the ticket.php file, we see that our the program will die if we have not put the **t_uid, object, and sign** JSON key.

Great, but what should we put inside?

We need to continue to read the check.php file a bit before to answer this question.

There is a KEY got from the database which will be used to create a Signature object.

Let's grab it!

SCREENSHOT QRENCODE SQL INJECTION

SCREENSHOT output

//TO BE CONTINUE

SCREENSHOT OF VULNERABLE PART

The source code of the ticket.php file reveals a vulnerability: **A PHP Object Injection!**

But before to get it... take your car, and let's drive **a lot!**, because the final step is still far away! (Thank you so much GHOZT for that!)

## **Step 5: Create your most beautiful object to RCE!** //ID cmd

We know that ...

## **Step 6: Get the flag, and go drink a beer!** //RCE shell

ls /

cat /flag.txt

Flag is: FLAG

![Cheers](/verre-chope.jpg)
