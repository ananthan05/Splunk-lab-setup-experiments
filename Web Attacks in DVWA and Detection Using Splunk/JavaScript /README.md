##  Launching an Attack from Another PC in the LAN:

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/jssource.png" width="500">

We see that the Javascript code exists in the file <b>vulnerabilities/javascript/source/medium.js</b>, so let's check that source out too. We find the following code:

1 <code>function do_something(e){</code><br>
2 <code>  for(var t="",n=e.length-1;n>=0;n--)</code><br>
3 <code>    t+=e[n];</code><br>
4 <code>  return t</code><br>
5 <code>}</code><br>
6 <code>setTimeout(function(){</code><br>
7 <code>  do_elsesomething("XX")</code><br>
8 <code>},300);</code><br>
9 <code>function do_elsesomething(e){</code><br>
10 <code> document.getElementById("token").value=do_something(e+document.getElementById("phrase").value+"XX")</code><br>
11 <code>}</code><br>

Let's break down the code line-by-line:
<ol type="1">
  <li>This defines the function <code>do_something()</code>, which has one argument <code>e</code>.</li>
  <li>This iterates through <code>e</code> character-by-character in reverse order.</li>
  <li>This appends the current character to a new temporary string.</li>
  <li>This line returns the temporary string, which is <code>e</code> reversed.</li>
  <li>This closes out the <code>do_something()</code> function definition.</li>
  <li>This sets a timeout, which will execute the defined <code>function()</code> after a specified wait.</li>
  <li>This <code>function()</code> code calls the other function <code>do_elsesomething()</code>, defined below, with the argument "XX".</li>
  <li>This sets the timeout for <code>function()</code> to 300 milliseconds.</li>
  <li>This defines the function <code>do_elsesomething()</code>, which has one argument <code>e</code>. </li>
  <li>This sets the "token" value to the value returned by <code>do_something()</code> with an argument of <code>the input argument, concatenated with the value input into the challenge's form, concatenated with "XX"</code>.</li>
  <li>This closes out the <code>do_elsesomething()</code> function definition.</li>
</ol>

<h3><b>Crafting a New Exploit</b></h3>

All told, the above code sets the value of "token" to "XX" concatenated with the input form value, reversed, concatenated with "XX". So in order to win the challenge, we need to set "token" to "`XXsseccusXX`"!


Now, after creating the payload, intercept the request in Burp Suite after submitting it as 'success'.

![image](https://github.com/user-attachments/assets/a6d99e2e-51c7-4d5b-a1c0-ef263491a259)


Change the token from `token=XXeMegnahCXX` to `token=XXsseccusXX`. 

![image](https://github.com/user-attachments/assets/c40b65bc-ddaf-4512-b809-28c5d3797888)

then forward the request. The attack was successful.

![image](https://github.com/user-attachments/assets/910b54bc-111a-4cdf-b608-288119dde7d5)

## Next, we will detect the attack using Splunk
