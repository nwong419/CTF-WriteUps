# Lian_Yu TryHackMe: A Beginner Level Security Challenge

## Tools:

*Reconnaissance*

-   `nmap` : maps ports on IP addresses to check which services are running (port scanner)
    

-   [Nmap Cheat Sheet](https://www.stationx.net/nmap-cheat-sheet/)
    

*Enumeration*

-   `gobuster` : directory fuzzing (understand status codes)
    

-   [How to Scan Websites for Interesting Directories & Files with Gobuster](https://null-byte.wonderhowto.com/how-to/scan-websites-for-interesting-directories-files-with-gobuster-0197226/)
    
-   [HTTP - Status Codes](https://www.tutorialspoint.com/http/http_status_codes.htm)
    

Exploitation

-   `steghide`
    

Privilege Escalation

-   `pkexec`
    

----------

**Target IP Address: 10.10.221.107**

  

Okay so I started with a quick nmap scan to see open ports.

`nmap -sC -sV -O -T5 10.10.221.107`
![](https://lh3.googleusercontent.com/TmuCBZN9BLk08bLpUDJyo0MRmVfUQxbZ20pQyQt8cEtpWqJAmBfFLf7mPgMwQ6A1baRi4yzSfSspfb4HO95Hwu0cXPeTN1C34xie8HPYxtyRLatoc_xDRznJf0ga5Yg_nMcnn4hx)

-- 1. Port 21 running on TCP

-- 2. Port 22 running on TCP

-- 3. Port 80 running on TCP

-- 4. Port 111 running on TCP

  

Well I see that we have a web server so let’s visit the site at http://10.10.221.107. Wow what a cool Arrowverse site, but I want to explore more so time to enumerate!

  

![](https://lh3.googleusercontent.com/DOFX8a_rphPdlH3n8yenru2EXNeyk1Z0ItsB5ALm-NfaJgxlRho5dVHUSO_luhTEZ0ZBrhiQ7p8w-jsP13zapghFWXhDI0DyM7WMTVaqf1A9GpwD-UMOBnsYPgDFlRXfjyc3Rnvu)

  

I am going to use gobuster to perform a dictionary attack to see if I can find any web directories.

    gobuster dir -u http://10.10.221.107 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt

**Found: /island (Status: 301)**

  

So I continued to http://10.10.221.107/island and viewed the page source. Guess what I found… ‘vigilante’ hidden due to its color being set to white. (This could be a possible user).

  

![](https://lh5.googleusercontent.com/SlthE4dCRj_1PTNfJpCupKOtxrxKCkTFTBGGFq5SfmI4tY0DAFVkm9ItYDvCTjO4r5CSw_UBmz08KRNRjU-Md0InxeSRJEsTE3nF1XYPUrSbcpM3WfuLQk54pw09Egef-xYD2I_J)

Due to its status being 301 which means ‘Moved Permanently’ I believe that there will be more directories within it. Therefore I’m going to use gobuster again, this time against http://10.10.221.107/island

**Found: /2100 (Status: 301)**

  

I navigated to the new directory and pulled up the page source.

  
![](https://lh3.googleusercontent.com/WJQtfdakJDIUcvuIq9yuPSLXPkZVjRc3Lh3IZFowMamUF91chYItbx7lKvKJt-pwfaXd88IhDzwXJr8l4s1I8mxPwMd7qa6RrdS6IkJfchsode4aq_ZLCuYA-7yTb1dE-gwhMoUK)  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

There is a comment with an extension .ticket, so I’m going to do another enumeration specifying the extension (-x) with gobuster.

**Found: /green_arrow.ticket (Status: 301)**

  

![](https://lh5.googleusercontent.com/RTP74ZuHErxliBZ2MaS3jscT_gYfaA3naKMBDqRcVNqMmYuEH8j3a-uQf2MwBZr-hjVyJivtVWHLKxToUNuKLbJtaLsKAFsTx4vwGZQGYpKuejQf57oplHxqUhtfrzkP9QjKnpzc)

This looks like a password, but it seems to be encrypted. Let’s fire up CyberChef ([GCHQ CyberChef](https://gchq.github.io/CyberChef/))to decode the string. After trying a few operations I finally got it with Base65.

![](https://lh5.googleusercontent.com/XmRu0j7-GM3DUnLwq91XTYo9oWpTANxm4Mut83fBJ0RmZX4HBu53t1T1UW2wzv-gNrc7Bod7KnuPI3oduaN0pvTwqKwT12ch1VYDEQoZbB90cBipvBhBJ80CatSWOFuFIJ0Xd74o)

Well it seems I have a possible user/password combination with vigilante/!#th3h00d.

  

I saw Port 21 (ftp) up so lets try it with the possible credentials?

    ftp 10.10.221.107

Once logged in I listed the directories and files with ls -la and found 3 images:

-   Leave_me_along.png
    
-   Queen’s Gambit.png
    
-   aa.jpg
    

  
  
  
  
  

To give you a browser view:

![](https://lh6.googleusercontent.com/AQStN4pkIe8zDMCO09cb_KB85OYTXPewuJktZl8-rnX7KX68Eo1KTZvHKLzFTSpCuJ6T-O_Md2T0t4snpx0ltinOdcAn-HW6Lo34DHvN-iAuoSJmdFNLi6d3HgXEUGwgdv4UeUwo)

Well I’m curious to see what pictures they store so I start clicking on them. However “Leave_me_alone.png” has an error and is unable to display the picture. Time to find out why!

> file Leave_me_alone.png
> 
> | Leave_me_alone.png : data

I want to get a hex dump of the first few lines to check the file signature (magic number). I see that the magic number is not what it is supposed to be for png images. ([List of file signatures](https://www.wikiwand.com/en/List_of_file_signatures))

    xxd Leave_me_alone.png | head

![](https://lh3.googleusercontent.com/w1-4di4F1DAYXq7pFUMQm75rLhZUF-Ki7DZazKHEK4eIAgGH7edDAmofNCegu7AgsjjpPV9H3GcXLzkEzDSZACVaVYKGwI94dG3l8XefAMLvHrrFgPau1BB9CgmO2SjevPLCBpNF)

-   For png files: **89 50 4E 47 0D 0A 1A 0A**
    

Therefore I must change it to the correct magic numbers.

    hexedit Leave_me_alone.png

After I fix it and open the image I see this:![](https://lh5.googleusercontent.com/5rUiBcmts3QRRwsPL5EvLnQSsvbWGMQjCLaMkquHXrTGe9vVNeuEauqF4_i-jEql4jH_kVKshyiaIbKU7K_8B7_nulq7fqmbE03i6GGDytzcDULWmAU08UFPCoLMNXgYKINQk9cS)

Well this seems to be like the perfect password :) I tried logging into SSH with vigilante and this new password but it didn’t work, so I have to keep searching.

> steghide extract -sf aa.jpg
> 
> | wrote extracted data to “ss.zip”

The passphrase was the password we found in “Leave_me_alone.png.” I then proceeded to unzip the file and found two text files.

-   passwd.txt
    
-   shado
    

![](https://lh6.googleusercontent.com/f80xoCkRMqHF1Qt3PIYUv1x-H7CRpigsrgtFRhVozwPsE_P2nzNrEfQJLuDkK3pYJai_OvFMTxhB96C0DbA5Qr5eGbgYXU045eBldtxSTVxQDXkDOf6PR9_o50lXaRETo86IH_yB)

A password seemed to be stored in ‘shado.’ I want to try ssh with the user ‘slade’ since I found their directory but could not access it.

    ssh slade@10.10.221.107

![](https://lh4.googleusercontent.com/PdSpqLV6NEwO776Pw6nub5mV4sZfSOyQzditCzgOXzixYXm07CgKpAfB232xzykT_AXOpzGXEUiGq1NWK42BkCsB-Hxc5iaRvOEJ4PxMvoriUrQYoNZmsXRz8Z2kpUkzJ5nUJsRx)

![](https://lh4.googleusercontent.com/36DR-_krz0KHQx9wSbeeN6Wd3mk8j-zTYsSNV9ZYPScCaGCLf2ifhHlqkyO8wROyPeXMgfzBgWLtGcXbYuntGol-cI-01BcuRty7KXjCtLMqTm5jAR6rOiJAe5qmVTCqFWdQcwtv)

Now I want to see if the user has sudo permissions (Privilege Escalation).

    sudo -l

> |User slade may run the following commands on LianYu:
> 
> (root) PASSWD: /usr/bin/pkexec

Using the man command I see that the pkexec command is used to execute a command as another user. Sweet! Let’s try running as root.

![](https://lh6.googleusercontent.com/B_3_yPrGIl1QMwWEFQtWKSNF9kBDzls6RlXzuj7sRCMbrHIdBxvLwpXifJ870VT1rPcyu9dFHn26XrXCHYse856q7yE6UR2V0pT4YKjrUMIDQBYWCbXOn48kugqiNwvHXQJbyBnM)

![](https://lh5.googleusercontent.com/ysdaR0ze-dRETSYIogOZanIMxn8EgvGBsNQgHN02fDWt4ucZFN2iB786Zc8UBBOuK4TYfKUSso8D8i-QiphZeMOT_RPCwkQiaTt0tGUhpeXRB-RLt0Io1ruJsMUMp_dlo6HOvFoo)

  Thanks for reading! 

**By: Nicole Wong**
