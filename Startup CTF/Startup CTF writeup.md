<h1>Startup CTF</h1>
<ul>
  <li>Date: 22.06.2021</li>
  <li>Difficulty: Easy</li>
  <li>Location: <a href="https://tryhackme.com/room/startup">TryHackMe</a>
</ul>
<h2>Flag 1</h2>
First thing I always do is check if We have something interesting on http server:
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/index.png"></img>
As We can see there is nothing interesting here for now. Next We want to start <b>nmap</b> and <b>gobuster</b> on our target. <b>Nmap</b> gives us information about services running on target machine and it can lead us to vulnerabilities we can use to get access. <b>Gobuster</b> is looking for directories 'hidden' on http server.
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/nmap.png"></img>
Nmap found 3 running services:
<ul>
  <li>http on port 80- We already knew about that</li>
  <li>ssh on port 22- it is standard port for ssh</li>
  <li>ftp on port 21- and that's interesting, beacuse ftp is often vulnerable</li>
</ul>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/gobuster.png"></img>
<b>Kali Linux</b> has built in wordlists stored in <i>/usr/share/wordlists</i>. For our purpose I've used <i>common.txt</i> which, as its name suggests, contains commonly used http directories. Status 403 means that this page is forbidden, 301 means redirect and 200 that page is accessible. We are interested only with status 200 and 301, <i>/index.html</i> is a main page that We have already seen, so We need to check <i>/files</i>.<br><br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/files.png"></img>
Yey <i>ftp</i> directory, thats really good for us, but let's check other things first.<br><br>
<i>notice.txt</i><br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/notice.png"></img>
<i>important.jpg</i><br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/important.png" height="400" width="500"></img><br><br>
Unfortunately there is nothing important, except that 'Maya' may be a username.
<i>ftp/</i> is empty for now and We have to change it.
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/ftp.png"></img>
It's time to switch to the terminal. We need to connect somehow to ftp server but we know only one probable username 'Maya' but we don't know password, so We can try something else and that is connecting as an anonymous user:
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/ftp1.png"></img>
We don't put anything in password, just hit enter and here we are. First We should check in what directory Weare and as You can see it is the same as http server, using <b>dir</b> command shows all files and directories in current location. Next We want to switch to ftp directory with <b>cd</b> command. In almost every CTF that includes ftp We can encounter vulnerability that allows user to send php file to the server, it is very dangerous because it allows remote code execution on server. To check it We make an empty <i>php</i> file and try to upload it with <b>put</b> command and it works.<br><br>

Now We have to create <i>php</i> file with reverse shell, I am always using code from <a href="https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php">HERE</a>. Now copy that code in our file and We have to change ip (our ip and not target ip) and if We want We can also change port but that isn't necessary.
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/reversephp.png"></img><br>
We can upload it now, in the same way we did with test file. Ok We have a reverse shell but we need to set listener on Our system, I am using <b>netcat</b> for that.<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/nc.png"></img><br>
1234 on the end of <b>netcat</b> command is port on which we are listening and it should be the same we set in reverse shell. If everything is ok we should see the file with reverse shell in <i>ftp/</i> directory and after simple click listener should react:
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/reversedone.png"></img><br>

We have connection but as You can see I put there some python code, it isn't necessary, it only makes shell a bit prettier :D Now We want to list everything in current directory with <b>ls</b> and as a result We can see <i>recipe.txt</i> which contains answer to the first question:<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/1flag.png"></img><br>
<h2>Second flag</h2>
Now We want to change directory to <i>home</i> and list everything there. <br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/permdenied.png"></img><br>
There is the directory named <i>lennie</i> but We aren't permitted to open it. We missed one interesting directory before executing <b>cd home</b> which is <i>incidents</i> and there is <i>suspicious.pcapng</i>. We have to get it from there somehow, the best idea in my opinion is using python to create simple http server:<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/server.png"></img><br>
4321 is of course random port selected to run our server and now we can easily download that file. There are 2 possible ways to progress now: use <b>strings</b> on that file, or analyze traffic in wireshark. Both are valid and will lead to:<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/wiresharktcpflow.png"></img><br>
We have to check if it is Lennie's password:<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/lennieuser.png"></img><br>
It works, now We are lennie so we can open his directory in which We can find <i>user.txt</i> cantaining second flag.
<br><h2>Third Flag</h2>
Let's check lennie's directory more precisely:<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/documents.png"></img><br>
Nothing interesting here, time for <i>scripts</i> now:<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/scripts.png"></img><br>
<i>planner.sh</i> makes changes in <i>startup_list.txt</i> and then uses <i>print.sh</i> to print that job is done. We can't modify <i>planner.sh</i> but we can do it with <i>planner.sh</i>. Again We want to use reverse shell but this time its bash version, I've used <a href="https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet">THIS</a> one, We have to set another listener as well:<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/bashscript.png"></img><br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/rootnc.png"></img><br>
Now we have root access and we can check root.txt:<br>
<img src="https://github.com/Brodek-sec/CTF-Write-up/blob/main/Startup%20CTF/photos/roottxt.png"></img><br>
<br>
I hope my writeup was useful and that You could learn something from it. ^_^ 
