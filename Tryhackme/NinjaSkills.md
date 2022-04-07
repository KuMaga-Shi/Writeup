# Ninja Skills

## About the CTF
Steganographyの基本を学習するためのルーム.
Steganography

## Writeup
---
### Task1 Ninja Skills  
マシンのいろんな場所に計12個のファイルが散りばめられている。  
とりあえず各ファイルの名前がわかっているので、どこにあるか探してみる。  
適当なディレクトリにファイルのリストを書いたファイルを作る。
```
echo -e "8V2L\nbny0\nc4ZX\nD8B3\nFHl1\noiMO\nPFbD\nrmfX\nSRSq\nuqyw\nv2Vb\nX1Uy" > /tmp/list.txt
```

`find`コマンドの結果を`grep`してファイルの場所を調べます。
```
find / 2>/dev/null | grep -f /tmp/list.txt > /tmp/file_location.txt
```
結果は`/tmp/file_location.txt`に保存しています。

```
cat /tmp/file_location.txt

/mnt/D8B3
/mnt/c4ZX
/var/FHl1
/var/log/uqyw
/opt/PFbD
/opt/oiMO
/media/rmfX
/etc/8V2L
/etc/ssh/SRSq
/home/v2Vb
/X1Uy
```
`bny0`がない気がするけど、まぁいいか。。。

問題を解いていきます！

- Which of the above files are owned by the best-group group(enter the answer separated by spaces in alphabetical order)

```
for x in `cat /tmp/file_location.txt`; do ls -la ${x} ;done

-rw-rw-r-- 1 new-user best-group 13545 10月 23  2019 /mnt/D8B3
-rw-rw-r-- 1 new-user new-user 13545 10月 23  2019 /mnt/c4ZX
-rw-rw-r-- 1 new-user new-user 13545 10月 23  2019 /var/FHl1
-rw-rw-r-- 1 new-user new-user 13545 10月 23  2019 /var/log/uqyw
-rw-rw-r-- 1 new-user new-user 13545 10月 23  2019 /opt/PFbD
-rw-rw-r-- 1 new-user new-user 13545 10月 23  2019 /opt/oiMO
-rw-rw-r-- 1 new-user new-user 13545 10月 23  2019 /media/rmfX
-rwxrwxr-x 1 new-user new-user 13545 10月 23  2019 /etc/8V2L
-rw-rw-r-- 1 new-user new-user 13545 10月 23  2019 /etc/ssh/SRSq
-rw-rw-r-- 1 new-user best-group 13545 10月 23  2019 /home/v2Vb
-rw-rw-r-- 1 newer-user new-user 13545 10月 23  2019 /X1Uy
```

`D8B3 v2Vb`

- Which of these files contain an IP address?
```
while read file; do echo ${file} &&  cat ${file} | grep -E '[0-9]{,3}\.[0-9]{,3}\.[0-9]{,3}\.[0-9]{,3}' ;done < /tmp/file_location.txt

/mnt/D8B3
/mnt/c4ZX
/var/FHl1
/var/log/uqyw
/opt/PFbD
/opt/oiMO
wNXbEERat4wE0w/O9Mn1.1.1.1VeiSLv47L4B2Mxy3M0XbCYVf9TSJeg905weaIk
/media/rmfX
/etc/8V2L
/etc/ssh/SRSq
/home/v2Vb
/X1Uy
```
`oiMO`

- Which file has the SHA1 hash of 9d54da7584015647ba052173b84d45e8007eba94
```
find / -name 8V2L-or -name bny0 -or -name c4ZX -or -name D8B3 -or -name FHl1 -or -name oiMO -or -name PFbD -or -name rmfX -or -name SRSq -or -name uqyw -or -name v2Vb -or -name X1Uy 2>/dev/null | xargs sha1sum

2c8de970ff0701c8fd6c55db8a5315e5615a9575  /mnt/D8B3
9d54da7584015647ba052173b84d45e8007eba94  /mnt/c4ZX
d5a35473a856ea30bfec5bf67b8b6e1fe96475b3  /var/FHl1
57226b5f4f1d5ca128f606581d7ca9bd6c45ca13  /var/log/uqyw
256933c34f1b42522298282ce5df3642be9a2dc9  /opt/PFbD
5b34294b3caa59c1006854fa0901352bf6476a8c  /opt/oiMO
4ef4c2df08bc60139c29e222f537b6bea7e4d6fa  /media/rmfX
acbbbce6c56feb7e351f866b806427403b7b103d  /etc/ssh/SRSq
7324353e3cd047b8150e0c95edf12e28be7c55d3  /home/v2Vb
59840c46fb64a4faeabb37da0744a46967d87e57  /X1Uy
```

`c4ZX`

- Which file contains 230 lines?

```
cat /tmp/file_location.txt | xargs wc

   209    209  13545 /mnt/D8B3
   209    209  13545 /mnt/c4ZX
   209    209  13545 /var/FHl1
   209    209  13545 /var/log/uqyw
   209    209  13545 /opt/PFbD
   209    209  13545 /opt/oiMO
   209    209  13545 /media/rmfX
   209    209  13545 /etc/8V2L
   209    209  13545 /etc/ssh/SRSq
   209    209  13545 /home/v2Vb
   209    209  13545 /X1Uy
```
全部209行。1つだけないファイルが230行ってことか。  
`bny0`

- Which file's owner has an ID of 502?
見えてるファイルで、ownerの選択肢は2つ。`new-user`か`newer-user`。  
（まぁ多分`X1Uy`が答えだろう）

```
id 

uid=501(new-user) gid=501(new-user) groups=501(new-user)
```

```
id newer-user

uid=502(newer-user) gid=503(newer-user) groups=503(newer-user)
```

`X1Uy`

- Which file is executable by everyone?  
これも1問目で答えが出ている。  
`8V2L`
