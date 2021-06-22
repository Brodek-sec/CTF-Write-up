<h1>Startup CTF</h1>
<ul>
  <li>Date: 22.06.2021</li>
  <li>Difficulty: Easy</li>
</ul>
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
We don't put anything in password, just hit enter and here we are. First We should check if We are in a directory that We saw on http server, using <i>dir</i> command shows all files and directories in current location. Next We want to switch to ftp directory with <i>cd</i> command. In almost every CTF that includes ftp We can encounter vulnerability that allows user to send php file to the server, it is very dangerous because it allows us to put there reverse shell.







<img src=""></img>
