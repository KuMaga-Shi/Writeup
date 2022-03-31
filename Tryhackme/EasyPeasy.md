# Easy Peasy

## About the CTF
NmapやGobusterなどのツールを使用してシステムに侵入する、

## Writeup
---  
### Task 1  Enumeration through Nmap
まずはnmap!
```
nmap -sV <IP> -p0-65535

~~~~~~~~~~
PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
Task1の問題はこれで全部答えられそう！

- 1.How many ports are open?  `3`
- 2.What is the version of nginx?  `1.16.1`
- 3.What is running on the highest port?  `Apache`

### Task 2  Compromising the machine
- 1.Using GoBuster, find flag 1.
gobusterを使用して隠されたディレクトリを探す。
```
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 100

/hidden               (Status: 301) [Size: 169]
/index.html           (Status: 200) [Size: 612]                                  
/robots.txt           (Status: 200) [Size: 43]  
```
`/hidden`のhttp response codeが301でリダイレクトされていることがわかる。  
`/hidden`に対して再度`gobuster`を実行する。
```
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 100

/index.html           (Status: 200) [Size: 390]
/whatever             (Status: 301) [Size: 169]
```
`/whatever`を発見。  
続けて`/whatever`に対して`gobuster`をかけてみるも、何も見つからない。  
バックグラウンドのイメージにヒントがあるのかなと思って、htmlをダウンロードしてみる。  
なんか意味深なものを発見。  
`<p hidden="">ZmxhZ3tmMXJzN19mbDRnfQ==</p>`
Base64でdecodeする。  
```
echo "ZmxhZ3tmMXJzN19mbDRnfQ==" | base64 -d

flag{f1rs7_fl4g}
```

一丁上がり！ `flag{f1rs7_fl4g}`

- 2.Further enumerate the machine, what is flag 2?
- 3.Crack the hash with easypeasy.txt, What is the flag 3?
- 4.What is the hidden directory?
- 5.Using the wordlist that provided to you in this task crack the hash. What is the password?
- 6.What is the password to login to the machine via SSH?
- 7.What is the user flag?
- 8.What is the root flag?