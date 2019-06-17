# Sthack 2019 - CTF - Ticket Checker

<u>Creator of the challenge:</u> Ghozt </br>
<u>Category:</u> WEB</br>
<u>Difficulty:</u> Medium </br>
</br>
##**Step 1: Smile to the camera to find the entry point**</br>
</br>
Reaching the website, we get a webpage allowing you to scan a ticket (QRCode) from your camera.</br>
Once done, we get the following error: **"Error - Could not parse json"**.</br>
SCREENSHOT ERROR BURP</br>
</br>
Hum... </br>
</br>
From Burp, we intercept the request and save it for later.</br>
We use the tool "qrencode" to create a QRCode to put the content I want inside.</br>
We start with an empty json:</br>
</br>
SCREENSHOT QRENCODE CMD</br>
*qrencode '{}' -o - | base64 -w 0*</br>
*-o -*: output directly on the standard output</br>
*-w 0* : disable line wrapping</br>
</br>
We put the output into the raw-data of the data image in the "**comment**" parameter.</br>
Be carefull of the encoding here! </br>
SCREENSHOT COMMENT PARAM</br>
</br>
OK ! Missing the key '<b>uid</b>' in the JSON.</br>
</br>
##**Step 2: Try to exploit this!**</br>
Let's add it...</br>
SCREENSHOT QRENCODE CMD ID = 1</br>
</br>
Now we get a correct output telling us that the ticket has already been used.</br>
But... what if instead of the id 1, we use one maybe unknown? </br>
SCREENSHOT QRENCODE CMD BIG ID </br>
</br>
GREAT database error!</br>
</br>
##**Step 3: Injection SQL - Integer Based**</br>
Let's try for some Injection SQL!</br>
I will pass throught all SQL Injection tested with single quote, double quote and so on... It is an interger one here!</br>
Finding the right number of columns...</br>
SCREENSHOT QRENCODE CMD</br>
SCREENSHOT SQL INJECTION ORDER BY OUTPUT</br>
</br>
Finding the current user, the database... </br>
SCREENSHOT QRENCODE CMD VERSION AND DB</br>
SCREENSHOT CURRENT VERSION AND DATABASE OUTPUT</br>
</br>
<b>Nice</b> but I can not do anything better with that? Actually yes, let's try to read some local files!</br>
SCREENSHOT QRENCODE CMD LOAD_FILE</br>
SCREENSHOT CURRENT LOAD_FILE /var/www/html/index.php OUTPUT</br>
</br>
##**Great!!!**</br>
Let's leak some code here!</br>
<br/>
We get the following source code:</br>
* check.php</br>
* ticket.php</br>
* dbb.php</br>
</br>
</br>
##**Step 4: Read the source code**</br>
SCREENSHOT OF CHECK.PHP</br>
SCREENSHOT OF TICKET.PHP</br>
On the ticket.php file, we see that our the program will die if we have not put the <b>t_uid, object, and sign</b> JSON key. </br>
Great, but what should we put inside?</br>
We need to continue to read the check.php file a bit before to answer this question.</br>
There is a KEY got from the database which will be used to create a Signature object.</br>
Let's grab it!</br>
SCREENSHOT QRENCODE SQL INJECTION</br>
SCREENSHOT output</br>
//TO BE CONTINUE
SCREENSHOT OF VULNERABLE PART</br>
The source code of the ticket.php file reveals a vulnerability: <b>A PHP Object Injection!</b></br>
But before to get it... take your car, and let's drive <b>a lot!</b>, because the final step is still far away! (Thank you so much GHOZT for that!)</br>
</br>
##**Step 5: Create your most beautiful object to RCE!**</br> //ID cmd
We know that ...</br>
</br>
##**Step 6: Get the flag, and go drink a beer!**</br> //RCE shell
ls /
cat /flag.txt
Flag is: FLAG</br>
![GitHub Logo](/verre-chope.jpg)
