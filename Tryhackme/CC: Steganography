CC: Steganography

### About the CTF
Steganographyの基本を学習するためのルーム.
Steganography

## Writeup
### Task2 Steghide
steghideのオプション等を確認しながら答える。
steghideのヘルプを参照してみる。
```
steghide --help

steghide version 0.5.1

the first argument must be one of the following:
 embed, --embed          embed data
 extract, --extract      extract data
 info, --info            display information about a cover- or stego-file
   info <filename>       display information about <filename>
 encinfo, --encinfo      display a list of supported encryption algorithms
 version, --version      display version information
 license, --license      display steghides license
 help, --help            display this usage information

embedding options:
 -ef, --embedfile        select file to be embedded
   -ef <filename>        embed the file <filename>
 -cf, --coverfile        select cover-file
   -cf <filename>        embed into the file <filename>
 -p, --passphrase        specify passphrase
   -p <passphrase>       use <passphrase> to embed data
 -sf, --stegofile        select stego file
   -sf <filename>        write result to <filename> instead of cover-file
 -e, --encryption        select encryption parameters
   -e <a>[<m>]|<m>[<a>]  specify an encryption algorithm and/or mode
   -e none               do not encrypt data before embedding
 -z, --compress          compress data before embedding (default)
   -z <l>                 using level <l> (1 best speed...9 best compression)
 -Z, --dontcompress      do not compress data before embedding
 -K, --nochecksum        do not embed crc32 checksum of embedded data
 -N, --dontembedname     do not embed the name of the original file
 -f, --force             overwrite existing files
 -q, --quiet             suppress information messages
 -v, --verbose           display detailed information

extracting options:
 -sf, --stegofile        select stego file
   -sf <filename>        extract data from <filename>
 -p, --passphrase        specify passphrase
   -p <passphrase>       use <passphrase> to extract data
 -xf, --extractfile      select file name for extracted data
   -xf <filename>        write the extracted data to <filename>
 -f, --force             overwrite existing files
 -q, --quiet             suppress information messages
 -v, --verbose           display detailed information

options for the info command:
 -p, --passphrase        specify passphrase
   -p <passphrase>       use <passphrase> to get info about embedded data

To embed emb.txt in cvr.jpg: steghide embed -cf cvr.jpg -ef emb.txt
To extract embedded data from stg.jpg: steghide extract -sf stg.jpg
 ```

#### 1.What argument allows you to embed data(such as files) into other files?
Ans.: embed
#### 2.What flag let's you set the file to embed?
Ans.: -ef
#### 3.What flag allows you to set the "cover file"?(i.e  the jpg)
Ans.: -cf
#### 4.How do you set the password to use for the cover file?
Ans.: -p
#### 5.What argument allows you to extract data from files?
Ans.: extract
#### 6.How do you select the file that you want to extract data from?
Ans.: -sf
#### 7.Given the passphrase "password123", what is the hidden message in the included "jpeg1" file.
以下のコマンドを実行する。
```
steghide extract -sf jpeg1.jpeg
```
passwordの入力を求められるので、password123を入力。
出力されたファイル（a.txt）の内容を確認
```
cat a.txt
```
Ans.:pinguftw

### Task3 zsteg
### Task4 Exiftool
### Task5 Stegoveritas
### Task6 Spectrograms
### Task7 The Final Exam

