## Launching an Attack from Another PC in the LAN:

<b>Objective:</b> Work out how the ID is generated and then infer the IDs of other system users.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/weakform.png" width="500">

Right away, the text tells us to look at our cookies, specifically one called "dvwaSession". And when I think cookies, I think the Firefox developer console. Let's pull that up via the keyboard with <code>Control+Shift+K</code>. Let's click the "Generate" button and enter the text <code>document.cookie</code> in the console. Here's what I see:

![image](https://github.com/user-attachments/assets/c064613e-a313-42f7-98a0-1d17856b9930)

So let's click the "generate" button a few times, then analyse <b>dvwaSession</b>:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/weakcookie.png" width="500">

Even without viewing the source, this number format will look familiar to any experienced *nix user - it's Unix time! It doesn't appear to have any further parameters, so let's run our generated cookie values though an <a href="https://www.epochconverter.com/" target="_blank">epoch time converter</a> and view the result:

<table style="width:100%">
  <tr>
    <th><b>Cookie Value</b></th>
    <th><b>Human-Readable Time (GMT)</b></th>
  </tr>
  <tr>
    <th>1557161489</th>
    <th>2019-05-06 16:51:29</th>
  </tr>
  <tr>
    <th>1557161505</th>
    <th>2019-05-06 16:51:45</th>
  </tr>
    <th>1557161513</th>
    <th>2019-05-06 16:51:53</th>
  </tr>
  </tr>
    <th>1557161521</th>
    <th>2019-05-06 16:52:01</th>
  </tr>
</table>

attack completed

## Next, we will detect the attack using Splunk
