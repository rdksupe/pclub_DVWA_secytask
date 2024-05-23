---


---

<h1 id="pclub-dvwa">Pclub DVWA</h1>
<p>DVWA stands for Damn Vulnerable Web Application , and yes the website was indeed vulnerable ;) . For the first flag a simple look at the source of the images rendered on the gallery pages reveals a possibility of LFI (Local File Inclusion) vulnerability , and it indeed happens to be true. A simple modification to the paths leads us to the routes.txt file.<br>
And we get our first flag :</p>
<pre><code>pclub{path_trav3rsa1_1s_fun} 
</code></pre>
<p><img src="https://s12.gifyu.com/images/Sf9mK.gif" alt="flag1"></p>
<p>For the next flag following the hint leads to the /ipDetails where a simple test for command injection via simple payload of |ifconfig reveals the flag.</p>
<pre><code>60:45:bd:af:3f:e5
</code></pre>
<p><img src="https://s12.gifyu.com/images/Sf9mv.md.gif" alt="flag2"></p>
<p>For the next flag possibilities of exploitation exists in the routes /secretary_login and /blogs . Going to secretary login route shows a simple login form which use multipart-form data for sending a POST request. Testing this route using SQlmap for Sqli reveals it to be non injectable. Hence we shift our attention to the /blogs route where for rendering of the blogs /getBlogDetail path is used. Putting this route to test indeed reveals that it is vulnerable to SQL Injection.</p>
<p>Further using the following commands in SQLMap allows us to get the list of DBs , using which we can query for the tables and the columns in those tables.</p>
<p><strong>For testing against SQLinjection :</strong></p>
<pre><code> sqlmap -u "http://74.225.254.195:2324/getBlogDetail?blog=1&amp;part=link"
</code></pre>
<p>For exploitation the following commands were used :<br>
<strong>List of Databases :</strong></p>
<pre><code>sqlmap -u "http://74.225.254.195:2324/getBlogDetail?blog=1&amp;part=link" --dbs
</code></pre>
<p>[<img src="https://i.postimg.cc/X7SPQf3s/image.png" alt="image.png">]<br>
<strong>List of Tables :</strong></p>
<pre><code>sqlmap -u "http://74.225.254.195:2324/getBlogDetail?blog=1&amp;part=link" -D pclub_secy_task --tables
</code></pre>
<p><strong>List of Columns in the User Table :</strong></p>
<pre><code>sqlmap -u "http://74.225.254.195:2324/getBlogDetail?blog=1&amp;part=link" -D pclub_secy_task -T USERS --columns
</code></pre>
<p><strong>Dumping Data from all available columns in the User Table:</strong></p>
<pre><code> sqlmap -u "http://74.225.254.195:2324/getBlogDetail?blog=1&amp;part=link" -D pclub_secy_task -T USERS -C user,password,userhash --dump
</code></pre>
<p><img src="https://gcdnb.pbrd.co/images/GmHycIFu12Br.png" alt="sqlmap output"><br>
After dumping the data in the csv format and opening it , we find a list of usernames and associated password hashes and username hashes , from the data in the hints table its clear these hashes are MD5 hashes . A quick search of these hashes on known hashes website <a href="http://Hashes.com">Hashes.com</a>. Reveals that all of these hashes are of passwords which have already been leaked in the internet before .</p>
<p><img src="https://gcdnb.pbrd.co/images/punnvoGcdu8x.png" alt="hashes"><br>
<img src="!%5B%5D(%60https://gcdnb.pbrd.co/images/n2bdDiBVzAXm.png?o=1%60)" alt="unhashed"><br>
After logging in with the user:pass of kaptaan we get the flag ,<br>
<img src="!%5B%5D(https://gcdnb.pbrd.co/images/oUCbPUjJZcrX.png?o=1)" alt="sqli_flag"></p>
<p>Logging in with the password obtained for ritvikg22 gets us to the link of the pwn_task , whereas logging in with amansg22 gets us the log of Aerial Robotics IITK. Uploading this image on <a href="https://www.aperisolve.com/">aperisolve</a> which is a website which checks an image against common steg tactics applied on the image reveals a QR code embedded inside it.</p>
<p><img src="https://i.postimg.cc/GtSZmN6r/image.png" alt="Flag_4"></p>
<p>Scanning that QR code gives the flag.</p>
<pre><code>pclub{data_1s_3v3ryth1ng)
</code></pre>

