# Ice: Exploiting Windows & Privilege Escalation
*Target Machine: 10.10.149.3*

 
The first thing I did was run a SYN scan. (while waiting I was looking into x86 Assembly Language mwhaha)

  

    nmap -sS -sV -O 10.10.149.3

  

Something to note is that one of the more interesting ports open is Microsoft Remote Desktop (MSRDP) and Icecast running on 8000.

![](https://lh6.googleusercontent.com/vp3v7ha2FooKfVKPjjlsttvk8JpnXBbRWudoxEOs30GBaIQwAM9uX63kDKenM42Xcdtigg4yth7i0YoosLE8F3rMYKRVVVC88kIdwrJpwL7f8GvaCJwfoh9i72rf2g3wAQvW2ghz)

Now let’s gain access into the vulnerable machine!! 😈

To tell you the truth, I have no idea what Icecast is, so Google my best friend I’m coming. [https://www.cvedetails.com/cve/CVE-2004-1561/](https://www.cvedetails.com/cve/CVE-2004-1561/)

> Vulnerability Type: Execute Code Overflow

  

Okay now that I know the vulnerability, I’m going to use an exploit module in Metasploit.

I’ve successfully established a meterpreter shell and will proceed to enumerate the machine and find potential privilege escalation paths.![](https://lh6.googleusercontent.com/6_HvQ_wGLQHuHymg2CYdizB_fc-G0lf2L7PSR66YBjwi755Ld4JDV0xE6myoLrUxPSsXGaK3KChpwalmwV9i9gKeQtmueeXEQdY7eXwhzu-7NSar4Bq14KjDyb9Dxq7GnrvCZNkI)

![](https://lh5.googleusercontent.com/5LDCzEB2LWi_dRxqkB_0_Fgt12iCOphbvKuEKD06abxfKjQKcdeZr5qVPsAMC69P2ssY4QMa05y3G2JEBBi5UuIcdWlXuticjy5VQKHAr5mAnLJhrDQKNRf-bxKx36Ia_IX1E01n)

`run /post/multi/recon/local_exploit_suggester` (btw the instructions say that this doesn’t work the best on x64 machines)

-   Will return potential escalation exploits
    

![](https://lh5.googleusercontent.com/e5JeCQuz3-3F-_1tQgKlV23vT4eeT32ap2wCd_8vmf0FYUDMuxE9kvadvqfJxJ7jZbxscsFKZXril9EouaFkmM9_saCmICZAxqjMTU2A7_xEwvBWxbZNhC4NbMdL8xlPkDwpZVvl)

Note: to background our current session use `background` or `CTRL + Z`

I then used `exploit/windows/local/bypassuac_eventvwr` to get privilege escalation and it was a success as I could use `getprivs`

![](https://lh6.googleusercontent.com/b1p03DFhpmdJCnB1sgcHKaQsg6DD6Yq2k_8LCsEAqHK5SVGsd-dOzK0fUgR8d22E9AGgJ2JNevBb6kk0UU2fzjxTkxCfV3pm7vXaJ-KlV5Y_sEDhZVf6p6TdtysZ6Nxt_R3Gl1le)

**Lesson time: What is the LSASS Service?**

According to the box it is “the service responsible for authentication within Windows.” However I still want more information, so I’m going to pause here and do my research.

-   Local Security Authority Subsystem Service (LSASS)
    
-   “Verifies users logging on to a Windows computer or server, handles password changes, and creates access tokens”
    

-   [https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service)
    

  

To interact with lsass we need to be *‘living in’* a process with the same architecture and permissions. When we take over a running process we load another shared library into the preexisting program (DLL) to embed our malicious code. Then we spawn a new thread that hosts our shell.

  

**Lesson time: What is DLL?**

-   Dynamic Link Library (DLL)
    
-   DLL is a “library that contains code and data that can be used by more than one program at the same time.”
    

-   [https://support.microsoft.com/en-us/help/815065/what-is-a-dll#:~:text=A%20DLL%20is%20a%20library,common%20dialog%20box%20related%20functions.](https://support.microsoft.com/en-us/help/815065/what-is-a-dll#:~:text=A%20DLL%20is%20a%20library,common%20dialog%20box%20related%20functions.)
    

  

Okay back to our image, I see that the spoolsv.exe fits the requirements. I am going to migrate to this process and see what user we are now.

    migrate -N spoolsv.exe

![](https://lh4.googleusercontent.com/rHZ9BZO80vaHdAC88NQXliN1jCOAAAgUw0wmK4Zqt8O8419VKLBhqdxRZWM5MSVptcHGHe9xo-TCMEdl5NesR59Ufn6wMFEMKmL0UlCrzn2yTP0MeFTVLaG3mE8z-AE3d6CIgK3v)

  

**Tool: Mimikatz**

-   Infamous password dumping tool
    
-   Allows us to create ‘golden ticket’ → authenticate anywhere with ease (component of Kerberos)
    

> load kiwi

`creds_all` to retrieve all credentials

  

Ooohhh what do I spy with my little eyes 👀 … a **PASSWORD!!**

  
![](https://lh3.googleusercontent.com/iY4RXsCBfJLEH5eKCIMR-EWEK5C2gbOy-0bW3EDvl-ESytBfyLxDhLSxorQJpgfA_gK9R-1I5aig2tHRLEaSKW4u8n0wgAAvvOy7KBVKsV4pSAg6ZqWDeDE7EDnDduuO3C9ZUgVF)  
  
  
  
  
  
  
  
  
  
  
  
  

To enable remote desktop run: `post/windows/manage/enable_rdp`

  

To explore **manual exploitation via exploit code** for icecast: [https://www.exploit-db.com/exploits/568](https://www.exploit-db.com/exploits/568)

  

Thanks for Reading!

  

**By: Nicole Wong**
