Download Link: https://assignmentchef.com/product/solved-eecs-388-project-2-web-security
<br>



Intro to Computer Security

<h1>Project 2: Web Security</h1>

This project is due on Wednesday, February 15 at 6p.m. and counts for 8% of your course grade. Late submissions will be penalized by 10% plus an additional 10% every 5 hours until received. Late work will not be accepted after 19.5 hours past the deadline. If you have a conflict due to travel, interviews, etc., please plan accordingly and turn in your project early.

This is a group project; you will work in teams of two and submit one project per team. Please find a partner as soon as possible. If have trouble forming a team, post to Piazza’s partner search forum. The final exam will cover project material, so you and your partner should collaborate on each part.

The code and other answers your group submits must be entirely your own work, and you are bound by the Honor Code. You may consult with other students about the conceptualization of the project and the meaning of the questions, but you may not look at any part of someone else’s solution or collaborate with anyone outside your group. You may consult published references, provided that you appropriately cite them (e.g., with program comments), as you would in an academic paper. Solutions must be submitted electronically via Canvas, following the submission checklist below.

<h1>Introduction</h1>

In this project, we provide an insecure website, and your job is to attack it by exploiting three common classes of vulnerabilities: cross-site scripting (XSS), cross-site request forgery (CSRF), and SQL injection. You are also asked to exploit these problems with various flawed defenses in place. Understanding how these attacks work will help you better defend your own web applications.

<h2>Objectives</h2>

<ul>

 <li>Learn to spot common vulnerabilities in websites and to avoid them in your own projects.</li>

 <li>Understand the risks these problems pose and the weaknesses of naive defenses.</li>

 <li>Gain experience with web architecture and with HTML, JavaScript, and SQL programming.</li>

</ul>

<h2>Read this First</h2>

This project asks you to develop attacks and test them, with our permission, against a target website that we are providing for this purpose. Attempting the same kinds of attacks against other websites

without authorization is prohibited by law and university policies and may result in <em>fines, expulsion, and jail time</em>. You must not attack any website without authorization! Per the course ethics policy, you are required to respect the privacy and property rights of others at all times, <em>or else you will fail the course. </em>See the “Ethics, Law, and University Policies” section on the course website.

<h1>Target Website</h1>

A startup named <strong>B</strong><strong>UNGLE! </strong>is about to launch its first product—a web search engine—but their investors are nervous about security problems. Unlike the Bunglers who developed the site, you took EECS 388, so the investors have hired you to perform a security evaluation before it goes live. <strong>B</strong><strong>UNGLE! </strong>is available for you to test at <a href="https://eecs388.org/project2/">https://eecs388.org/project2/.</a>

The site is written in Python using the <a href="http://www.bottlepy.org/">Bottle</a> web framework. Although Bottle has built-in mechanisms that help guard against some common vulnerabilities, the Bunglers have circumvented or ignored these mechanisms in several places. If you wish, you can download and inspect the Python source code at <a href="https://eecs388.org/!/static/project2/bungle-src.tar.gz">https://eecs388.org/!/static/project2/bungle-src.tar.gz,</a> but this is not necessary to complete the project.

In addition to providing search results, the site accepts logins and tracks users’ search histories. It stores usernames, passwords, and search history in a MySQL database.

Before being granted access to the source code, you reverse engineered the site and determined that it replies to five main URLs: /, /search, /login, /logout, and /create. The function of these URLs is explained below, but if you want an additional challenge, you can skip the rest of this section and do the reverse engineering yourself.

Main page (<strong>/</strong>) The main page accepts GET requests and displays a search form. When submitted, this form issues a GET request to /search, sending the search string as the parameter “q”.

If no user is logged in, the main page also displays a form that gives the user the option of logging in or creating an account. The form issues POST requests to /login and /create.

Search results (<strong>/search</strong>) The search results page accepts GET requests and prints the search string, supplied in the “q” query parameter, along with the search results. If the user is logged in, the page also displays the user’s recent search history in a sidebar.

Note: Since actual search is not relevant to this project, you might not receive any results.

Login handler (<strong>/login</strong>) The login handler accepts POST requests and takes plaintext “username” and “password” query parameters. It checks the user database to see if a user with those credentials exists. If so, it sets a login cookie and redirects the browser to the main page. The cookie tracks which user is logged in; manipulating or forging it is not part of this project.

Logout handler (<strong>/logout</strong>) The logout handler accepts POST requests. It deletes the login cookie, if set, and redirects the browser to the main page.

Create account handler (<strong>/create</strong>) The create account handler accepts POST requests and receives plaintext “username” and “password” query parameters. It inserts the username and password into the database of users, unless a user with that username already exists. It then logs the user in and redirects the browser to the main page.

Note: The password is neither sent nor stored securely; however, none of the attacks you implement should depend on this behavior. You should choose a password that other groups will not guess, but never use an important password to test an insecure site!

<h1>Guidelines</h1>

Virtual Machine For this project, we’d like you to do all your testing within a provided VM, which can be found at <a href="https://eecs388.org/!/dist/388-proj2-vm.ova">https://eecs388.org/!/dist/388-proj2-vm.ova.</a> The password for this VM is <strong>project2</strong>. If you do not already have VirtualBox, please download it here <a href="https://www.virtualbox.org/wiki/Downloads">https://www.virtualbox. </a><a href="https://www.virtualbox.org/wiki/Downloads">org/wiki/Downloads</a> and import the provided machine. This VM provides Firefox 41 (the browser

we will use for grading) and a method for hosting a local HTTPS server (necessary for Part 3). Browser versions have slight variations in their behavior that may affect your XSS and CSRF attacks, so we highly recommend that you develop and test your solutions within the VM.

Defense Levels The Bunglers have been experimenting with some naïve defenses, so you also need to demonstrate that these provide insufficient protection. In Parts 2 and 3, the site includes drop-down menus at the top of each page that let you change the CSRF and XSS defenses that are in use. The solutions you submit must override these selections by including the csrfdefense=<em>n </em>or xssdefense=<em>n </em>parameter in the target URL, as specified in each task below. You may not attempt to subvert the mechanism for changing the level of defense in your attacks. Be sure to test your solutions with the appropriate defense levels!

In all parts, you should implement the simplest attack you can think of that defeats the given set of defenses. In other words, do not simply attack the highest level of defense and submit that attack as your solution for all defenses. You do not need to combine the vulnerabilities, unless explicitly stated.

Resources    The Firefox web developer tools will be very helpful for this project, particularly the

JavaScript console and debugger, DOM inspector, and network monitor. To open these tools, click the Developer button in the Firefox menu. See <a href="https://developer.mozilla.org/en-US/docs/Tools">https://developer.mozilla.org/en-US/docs/Tools. </a>Note that there is also a special version of the browser called Firefox Developer Edition; you’re welcome to use it to develop and test, but we will be grading with the normal edition of Firefox.

Although general purpose tools are permitted, your are not allowed to use tools that are designed to automatically test for vulnerabilities.

Your solutions will involve manipulating SQL statements and writing web code using HTML,

JavaScript, and the jQuery library. You should search the web for answers to basic how-to questions. There are many fine online resources for learning these tools. Here are a few that we recommend:

<table width="648">

 <tbody>

  <tr>

   <td width="177">SQL Tutorial</td>

   <td width="471"><a href="http://sqlzoo.net/wiki/SQL_Tutorial">http://sqlzoo.net/wiki/SQL_Tutorial</a></td>

  </tr>

  <tr>

   <td width="177">SQL Statement Syntax</td>

   <td width="471"><a href="https://dev.mysql.com/doc/refman/5.6/en/sql-syntax.html">https://dev.mysql.com/doc/refman/5.6/en/sql-syntax.html</a></td>

  </tr>

  <tr>

   <td width="177">Introduction to HTML</td>

   <td width="471"><a href="https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Introduction">https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Introduction</a></td>

  </tr>

  <tr>

   <td width="177">JavaScript 101</td>

   <td width="471"><a href="http://webdesignfromscratch.com/html-css/js101/">http://webdesignfromscratch.com/html-css/js101/</a></td>

  </tr>

  <tr>

   <td width="177">Using jQuery Core</td>

   <td width="471"><a href="https://learn.jquery.com/using-jquery-core/">https://learn.jquery.com/using-jquery-core/</a></td>

  </tr>

  <tr>

   <td width="177">jQuery API Reference</td>

   <td width="471"><a href="https://api.jquery.com/">https://api.jquery.com</a></td>

  </tr>

  <tr>

   <td width="177">HTTP Made Really Easy</td>

   <td width="471"><a href="http://www.jmarshall.com/easy/http/">http://www.jmarshall.com/easy/http/</a></td>

  </tr>

 </tbody>

</table>

To learn more about SQL Injection, XSS, and CSRF attacks, and for tips on exploiting them, see: <a href="https://www.owasp.org/index.php/SQL_Injection_Prevention_Cheat_Sheet">https://www.owasp.org/index.php/SQL_Injection_Prevention_Cheat_Sheet </a><a href="https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet">https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet </a><a href="https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet">https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet </a><a href="https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet">https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet</a>

<h1>Part 1. SQL Injection</h1>

Your first goal is to demonstrate SQL injection attacks that log you in as an arbitrary user without knowing the password. In order to protect other students’ accounts, we’ve made a series of separate login forms for you to attack that aren’t part of the main <strong>B</strong><strong>UNGLE! </strong>site. For each of the following defenses, provide inputs to the target login form that successfully log you in as the user “victim”:

1.0 No defenses

Target: <a href="https://eecs388.org/project2/sqlinject0/">https://eecs388.org/project2/sqlinject0/ </a>Submission: sql_0.txt

1.1 Simple escaping

The server escapes single quotes (‘) in the inputs by replacing them with two single quotes.

Target: <a href="https://eecs388.org/project2/sqlinject1/">https://eecs388.org/project2/sqlinject1/</a>

Submission: sql_1.txt

1.2 Escaping and Hashing

The server uses the following PHP code, which escapes the username and applies the MD5 hash function to the password.

if (isset($_POST[‘username’]) and isset($_POST[‘password’])) {

$username = mysql_real_escape_string($_POST[‘username’]);

$password = md5($_POST[‘password’], true);

$sql_s = “SELECT * FROM users WHERE username=’$username’ and pw=’$password'”;

$rs = mysql_query($sql_s); if (mysql_num_rows($rs) &gt; 0) { echo “Login successful!”;

} else { echo “Incorrect username or password”; }

}

This is more difficult than the previous two defenses. You will need to write a program to produce a working exploit. You can use any language you like, but we recommend C.

Target: <a href="https://eecs388.org/project2/sqlinject2/">https://eecs388.org/project2/sqlinject2/ </a>Submissions: sql_2.txt and sql_2-src.tar.gz

1.3 The SQL [Extra credit]

This target uses a different database. Your job is to use SQL injection to retrieve:

<ul>

 <li>The name of the database</li>

 <li>The version of the SQL server</li>

 <li>All of the names of the tables in the database</li>

 <li>A secret string hidden in the database</li>

</ul>

Target: <a href="https://eecs388.org/project2/sqlinject3/">https://eecs388.org/project2/sqlinject3/</a>

Submission: sql_3.txt

For this part, the text file you submit should starts with a list of the URLs for all the queries you made to learn the answers. Follow this with the values specified above, using this format:

<em>URL</em>

<em>URL URL</em>

…

Name: <em>DB name</em>

Version: <em>DB version string</em>

Tables: <em>comma separated names</em>

Secret: <em>secret string</em>

What to submit For 1.0, 1.1, and 1.2, when you successfully log in as victim, the server will provide a URL-encoded version of your form inputs. Submit a text file with the specified filename containing only this line. For 1.2, also submit the source code for the program you wrote, as a gzipped tar file (sql_2-src.tar.gz). For 1.3, submit a text file as specified.

<h1>Part 2. Cross-site Request Forgery (CSRF)</h1>

Your next task is to demonstrate CSRF vulnerabilities against the login form, and <strong>B</strong><strong>UNGLE! </strong>has provided two variations of their implementation for you to test. Your goal is to construct attacks that surreptitiously cause the victim to log in to an account you control, thus allowing you to monitor the victim’s search queries by viewing the search history for this account. For each of the defenses below, create an HTML file that, when opened by a victim, logs their browser into <strong>B</strong><strong>UNGLE! </strong>under the account “attacker” and password “l33th4x”.

Your solutions should not display evidence of an attack; the browser should just display a blank page. (If the victim later visits <strong>B</strong><strong>UNGLE!</strong>, it will say “logged in as attacker”, but that’s fine for purposes of the project. After all, most users won’t immediately notice.)

2.0 No defenses

Target: <a href="https://eecs388.org/project2/login?csrfdefense=0&amp;xssdefense=4">https://eecs388.org/project2/login?csrfdefense=0&amp;xssdefense=4 </a>Submission: csrf_0.html

2.1 Token validation

The server sets a cookie named csrf_token to a random 16-byte value and also includes this value as a hidden field in the login form. When the form is submitted, the server verifies that the client’s cookie matches the value in the form. You are allowed to exploit the XSS vulnerability from Part 3 to accomplish your goal.

Target: <a href="https://eecs388.org/project2/login?csrfdefense=1&amp;xssdefense=0">https://eecs388.org/project2/login?csrfdefense=1&amp;xssdefense=0 </a>Submission: csrf_1.html

2.2 Token validation, without XSS [Extra credit] Accomplish the same task as in 2.1 without using XSS.

Target: <a href="https://eecs388.org/project2/login?csrfdefense=1&amp;xssdefense=4">https://eecs388.org/project2/login?csrfdefense=1&amp;xssdefense=4 </a>Submission: csrf_2.html

This challenge is hard. We think it requires finding a 0-day vuln or a bug in our code.

What to submit For each part, submit an HTML file with the given name that accomplishes the specified attack against the specified target URL. The HTML files you submit may embed inline JavaScript and load jQuery from the URL <a href="https://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js">http://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery. </a><a href="https://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js">min.js.</a> It is also permissible to load the jQuery cookies library from <a href="https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js">https://cdnjs.cloudflare.com/ </a><a href="https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js">ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js.</a> Otherwise, your code must be self contained. Test your solutions by opening them as local files in the browser in the provided VM.

Note: Since you’re sharing the attacker account with other students, we’ve hard-coded it so the search history won’t actually update. You can test with a different account you create to see the history change.

<h1>Part 3. Cross-site Scripting (XSS)</h1>

Your final goal is to demonstrate XSS attacks against the <strong>B</strong><strong>UNGLE! </strong>search box, which does not properly filter search terms before echoing them to the results page. For each of the defenses below, your goal is to construct a URL that, when loaded in the victim’s browser, correctly executes the specified payload. We recommend that you begin by testing with a simple payload (e.g., alert(0);), then move on to the full payload. Note that you should be able to implement the payload once, then use different means of encoding it to bypass the different defenses.

<h2>Payload</h2>

The payload (the code that the attack tries to execute) will be to steal the username and the most recent search the real user has performed on the <strong>B</strong><strong>UNGLE! </strong>site. When a victim visits the URL you create, these stolen items should be sent to the attacker’s server for collection. For purposes of grading, your attack should report these events by loading the URL: https://localhost:31337/stolen?user=<em><sub>username</sub></em>&amp;last_search=<em><sub>last_search</sub></em>

You can test receiving this data within the provided VM by running this command at the shell:

$ cd /Documents; python run_server.py

and observing the HTTPS GET request that your payload generates. To further test that your HTTPS server is running, try navigating to <a href="https://localhost:31337/">https://localhost:31337.</a>

<h2>Defenses</h2>

There are five levels of defense. In each case, you should submit the simplest attack you can find that works against that defense; you should not simply attack the highest level and submit your solution for that level for every level. Try to use a different technique for each defense. The Python code that implements each defense is shown below, along with the target URL and the filename you should submit. 3.0 No defenses

Target: <a href="https://eecs388.org/project2/search?xssdefense=0">https://eecs388.org/project2/search?xssdefense=0 </a>Submission: xss_0.txt

For 3.0 only, also submit a human-readable version of your payload code (as opposed to the form encoded into the URL). Save it in a file named xss_payload.html.

3.1 Remove “script” filtered = re.sub(r”(?i)script”, “”, input)

Target: <a href="https://eecs388.org/project2/search?xssdefense=1">https://eecs388.org/project2/search?xssdefense=1</a>

Submission: xss_1.txt

3.2 Remove several tags filtered = re.sub(r”(?i)script|&lt;img|&lt;body|&lt;style|&lt;meta|&lt;embed|&lt;object”, “”, input)

Target: <a href="https://eecs388.org/project2/search?xssdefense=2">https://eecs388.org/project2/search?xssdefense=2 </a>Submission: xss_2.txt 3.3 Remove some punctuation filtered = re.sub(r”[;’”]”, “”, input)

Target: <a href="https://eecs388.org/project2/search?xssdefense=3">https://eecs388.org/project2/search?xssdefense=3 </a>Submission: xss_3.txt 3.4 Encode &lt; and &gt; [Extra credit] filtered = input.replace(“&lt;“, “&amp;lt;”).replace(“&gt;”, “&amp;gt;”)

Target: <a href="https://eecs388.org/project2/search?xssdefense=4">https://eecs388.org/project2/search?xssdefense=4</a>

Submission: xss_4.txt

This challenge is hard. We think it requires finding a 0-day vuln or a bug in our code.

What to submit Your submission for each level of defense will be a text file with the specified filename that contains a single line consisting of a URL. When this URL is loaded in a victim’s browser, it should execute the specified payload against the specified target. The payload encoded in your URLs may embed inline JavaScript and load jQuery from the URL <a href="https://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js">http://ajax.googleapis.com/ </a><a href="https://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js">ajax/libs/jquery/2.1.4/jquery.min.js.</a> It is also permissible to load the jQuery cookies library from <a href="https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js">https://cdnjs.cloudflare.com/ajax/libs/jquery-cookie/1.4.1/jquery.cookie.min.js.</a> Otherwise, your code must be self contained. Test your solutions with the browser in the provided VM.

<h1>Part 4. Writeup: Better Defenses</h1>

For each of the three kinds of attacks (SQL injection, CSRF, and XSS), write a paragraph of advice for the <strong>B</strong><strong>UNGLE! </strong>developers about what techniques they should use to defend themselves. Place these paragraphs in a file entitled “writeup.txt”.

What to submit       A text file named writeup.txt containing your security recommendations.

<h1>Submission Checklist</h1>

Upload to Canvas a gzipped tar file (.tar.gz) named project2.<em>uniqname1</em>.<em>uniqname2</em>.tar.gz that contains only the files listed below. These will be autograded, so make sure you have the proper filenames and behaviors. You can generate the tarball at the shell using this command:

tar -zcf project2.<em><sub>uniqname1</sub></em>.<em><sub>uniqname2</sub></em>.tar.gz sql_[0123].txt 

sql_2-src.tar.gz csrf_[012].html xss_payload.html xss_[01234].txt writeup.txt

Where applicable, your solutions may contain embedded JavaScript. They are allowed to load only the exact Javascript libraries specified above, and must be otherwise self-contained. Test your solutions with the browser provided in the VM, which we will use for grading.

The solutions you submit for parts 2 and 3 must include the csrfdefense=<em>n </em>or xssdefense=<em>n </em>parameter in the target URL, as specified in each task. You may not attempt to subvert the mechanism for changing the level of defense in your attacks.

<h2>Part 1: SQL Injection</h2>

For parts 1.0, 1.1, and 1.2, submit text files that contain the strings provided by the server when your exploits work. For 1.2, also submit a tarball containing the source code you wrote to produce your solution. For 1.3, submit a text file as specified in the problem statement. Make sure your text files are actual .txt files or we may not be able to grade your work.

<table width="340">

 <tbody>

  <tr>

   <td width="159">sql_0.txt</td>

   <td width="180">1.0 No defenses</td>

  </tr>

  <tr>

   <td width="159">sql_1.txt</td>

   <td width="180">1.1 Simple escaping</td>

  </tr>

  <tr>

   <td width="159">sql_2.txt</td>

   <td width="180">1.2 Escaping and Hashing</td>

  </tr>

  <tr>

   <td width="159">sql_2-src.tar.gz</td>

   <td width="180">1.2 Escaping and Hashing</td>

  </tr>

  <tr>

   <td width="159">sql_3.txt<em>?</em></td>

   <td width="180">1.3 The SQL [Extra credit]</td>

  </tr>

 </tbody>

</table>

<h2>Part 2: CSRF</h2>

HTML files that, when loaded in a browser, immediately carry out the specified CSRF attack. Please remember to correctly specify the CSRF defense level in your URLs.

<table width="477">

 <tbody>

  <tr>

   <td width="159">csrf_0.html</td>

   <td width="318">2.0 No defenses</td>

  </tr>

  <tr>

   <td width="159">csrf_1.html</td>

   <td width="318">2.1 Token validation</td>

  </tr>

  <tr>

   <td width="159">csrf_2.html<em>?</em></td>

   <td width="318">2.2 Token validation, without XSS [Extra credit]</td>

  </tr>

 </tbody>

</table>

<h2>Part 3: XSS</h2>

Text files, each containing a URL that, when loaded in a browser, immediately carries out the specified XSS attack. For 3.0, also submit the human-readable (non–URL-encoded) payload. Please remember to correctly specify the XSS defense level in your URLs. (If you have nested URLs, specify the defense levels in the nested ones too!)

<table width="382">

 <tbody>

  <tr>

   <td width="160">xss_payload.html</td>

   <td width="222">3.0 No defenses</td>

  </tr>

  <tr>

   <td width="160">xss_0.txt</td>

   <td width="222">3.0 No defenses</td>

  </tr>

  <tr>

   <td width="160">xss_1.txt</td>

   <td width="222">3.1 Remove “script”</td>

  </tr>

  <tr>

   <td width="160">xss_2.txt</td>

   <td width="222">3.2 Remove several tags</td>

  </tr>

  <tr>

   <td width="160">xss_3.txt</td>

   <td width="222">3.3 Remove some punctuation</td>

  </tr>

  <tr>

   <td width="160">xss_4.txt<em>?</em></td>

   <td width="222">3.4 Encode &lt; and &gt; [Extra credit]</td>

  </tr>

 </tbody>

</table>

<h2>Part 4: Writeup</h2>

One text file named writeup.txt containing an answer to the questions in part 4.

<em>? </em>These files are optional extra credit.