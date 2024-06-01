# Boblox

Navigate over to the creator of the Roblox Game, @LNC4RobloxDevSpy. Under the 'About' account description contains an ASCII-encoded text which when decoded provides us a pastebin link.

​![image](assets/image-20240312151446-rtj6p6u.png)​

​![image](assets/image-20240312151505-imx45mh.png)​

​![image](assets/image-20240312151526-1yx6e9m.png)​

pastebin.com/EZ27QwYn. This link gives us further context and information. It states that the nuclear launch codes are hidden in 3 roblox accounts, with redacted account IDs starting with 544787XXXX and the 'About' description of the roblox accounts starting with LagNCrash.

​![image](assets/image-20240312153408-yroypsf.png)​

Hence, I created a python script to brute force the desired account IDs. As shown below, one of the three account IDs is 5447870123. Upon visiting the account's profile, I realised the name of the account is 'LNC4RBXACC1' which may be a acronym for 'LagNCrash Roblox Account 1'.

```python
import requests

for a in range(1, 10000):
	bruteID = '544787' + format(a, "04")
	URI = 'https://www.roblox.com/users/' + bruteID + '/profile'
	x = requests.get(URI)
	print('Trying: ' + format(a, "04"))
	if('LagNCrash' in x.text):
		print('Account Found!')
		print(bruteID)
		print(URI)
		print('----------')
```

​![image](assets/image-20240312160046-jvpzew2.png)​

​![image](assets/image-20240312153739-l6c7pvx.png)​

Since the account name starts with LNC4RBXACC, I searched for other accounts starting with LNC4RBXACC, and managed to find the other 2 accounts this way!

​![image](assets/image-20240312160128-2nmwccu.png)​

​![image](assets/image-20240312160140-gecfqm4.png)​

​![image](assets/image-20240312160148-dr29txq.png)​

Looking at the account description, the string seems to be in binary. Converting it using CyberChef, the 3 strings combined provides a link to a github account named 'poopoopeepeesauce'.  
https://github.com/poopoopeepeesauce/

​![image](assets/image-20240312160312-iv5xi7h.png)​

Opening the link, we can see that the account has three repositories. After looking at all 3 repositories, I decided to look at the **commit history,**  eventually finding another pastebin link from the 'TelegraphMsgs' repository's second commit.  
https://pastebin.com/vm4bhNGb​

​![image](assets/image-20240312160520-fjj76ul.png)​

​![image](assets/image-20240312160559-6f217xf.png)​

Opening the pastebin link, we are provided with another message. The message contains the first half of the flag, as well as a link to a website made with Wix.  
https://soraalt2.wixsite.com/mysecretwebsite

​![image](assets/image-20240312160713-ih57tui.png)​

Exploring the website, the footer contains the message 'Send me an email 4 Flag: <REDACTED>'. In the 'about' section, there contains invisible text with 'Hint: Look at email'.

​![image](assets/image-20240312161205-p9l9s4l.png)​

​![image](assets/image-20240312161245-2kuvycb.png)​

Hence, I went back to the github account to find it's email. Searching online, I found a trick to find Github User Emails via commits made to the repository.

1. Navigate into a commit made.
2. In the URL, add .patch to the end of the URL

​![image](assets/image-20240312161424-elumd88.png)​

However, the email above *poopoopeepeesauce@users.noreply.github.com* is a noreply email which means that I had went out-of-scope.

After brainstorming for a while, I realised the second pastebin message had the Timestamp of **16th January 2024**. However, the first pastebin message had timestamps in the year **2059**.

Hence, I inferred that 16th January may be an important clue, and utilised **Wayback Machine** to possibly view an archive of the website on that date. Viewing the snapshot, I was able to find the desired email, *4hsarcngal@gmail.com*

​![image](assets/image-20240312161944-myhudtc.png)​

​![image](assets/image-20240312162025-qutpxhz.png)​

I then sent an email to the address, and received a reply containing an ID for discord. Using discordlookup.com, the ID seemed to be a Guild ID for a discord server. Lastly, I joined the discord server and found the flag!

​![image](assets/image-20240312162434-86cgevz.png)​

​![image](assets/image-20240312162501-oxue31l.png)​

​![image](assets/image-20240312162532-1qe4jkp.png)​

Flag: LNC24{In54n3_051nt_Ch4ll3ng3}

‍

References:

https://wizardsourcer.com/simple-way-to-find-github-user-emails/

https://web.archive.org/web/20240116111249/https://soraalt2.wixsite.com/mysecretwebsite

https://discordlookup.com/guild/1197367899883589703

‍
