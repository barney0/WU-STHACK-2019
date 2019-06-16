# Sthack 2019 - CTF 

Write UP of "Ticket Checker" </br>
<u>Creator:</u> Ghozt </br>
<u>Category:</u> WEB</br>
<u>Difficulty:</u> Medium </br>
</br>
<b><u>Step 1:</u></b></br>
</br>
Reaching the website, we get a webpage allowing you to scan a ticket (QRCode) from your camera.</br>
Once done, we get the following error: <b>"Error - Could not parse json"</b>.</br>
SCREENSHOT ERROR BURP</br>
</br>
Hum... </br>
</br>
From Burp, we intercept the request and save it for later.</br>
We use the tool "qrencode" to create a QRCode to put the content I want inside.</br>
We start with an empty json:</br>
</br>
SCREENSHOT QRENCODE CMD</br>
<i>qrencode '{}' -o - | base64 -w 0</i></br>
<i>-o - : output directly on the standard output</i></br>
<i>-w 0</i> : disable line wrapping</br>
</br>
We put the output into the raw-data of the data image in the "<b>comment</b>" parameter.</br>
Be carefull of the encoding here! </br>
SCREENSHOT COMMENT PARAM</br>
OK ! Missing the key '<b>uid</b>' in the JSON.</br>
Let's add it...</br>
SCREENSHOT QRENCODE CMD ID = 1</br>
Now we get a correct output telling us that the ticket has already been used.</br>
But... what if instead of the id 1, we use one maybe unknown? </br>
SCREENSHOT QRENCODE CMD BIG ID </br>
GREAT database error!</br>
Let's try for some Injection SQL!</br>
