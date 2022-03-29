#CC: Steganography

### About the CTF
Steganographyの基本を学習するためのルーム.
Steganography

## Writeup
---
### Task2 Steghide
steghideのオプションを確認しながら答える。
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
passwordの入力を求められるので、`password123`を入力。
出力されたファイル（a.txt）の内容を確認
```
cat a.txt
```
Ans.:pinguftw
---
### Task3 zsteg
zstegのオプションを確認
```
# zsteg -h


Usage: zsteg [options] filename.png [param_string]

    -c, --channels X                 channels (R/G/B/A) or any combination, comma separated
                                     valid values: r,g,b,a,rg,bgr,rgba,r3g2b3,...
    -l, --limit N                    limit bytes checked, 0 = no limit (default: 256)
    -b, --bits N                     number of bits, single int value or '1,3,5' or range '1-8'
                                     advanced: specify individual bits like '00001110' or '0x88'
        --lsb                        least significant BIT comes first
        --msb                        most significant BIT comes first
    -P, --prime                      analyze/extract only prime bytes/pixels
        --invert                     invert bits (XOR 0xff)
    -a, --all                        try all known methods
    -o, --order X                    pixel iteration order (default: 'auto')
                                     valid values: ALL,xy,yx,XY,YX,xY,Xy,bY,...
    -E, --extract NAME               extract specified payload, NAME is like '1b,rgb,lsb'

        --[no-]file                  use 'file' command to detect data type (default: YES)
        --no-strings                 disable ASCII strings finding (default: enabled)
    -s, --strings X                  ASCII strings find mode: first, all, longest, none
                                     (default: first)
    -n, --min-str-len X              minimum string length (default: 8)
        --shift N                    prepend N zero bits

    -v, --verbose                    Run verbosely (can be used multiple times)
    -q, --quiet                      Silent any warnings (can be used multiple times)
    -C, --[no-]color                 Force (or disable) color output (default: auto)

PARAMS SHORTCUT
	zsteg fname.png 2b,b,lsb,xy  ==>  --bits 2 --channel b --lsb --order xy
```

#### 1.How do you specify that the least significant bit comes first
Ans.: --lsb

#### 2.What about the most significant bit?
Ans.: --msb

#### 3.How do you specify verbose mode?
Ans.:-v

#### 4.How do you extract the data from a specific payload?
Ans.: -E

#### 5.In the included file "png1" what is the hidden message?
```
# zsteg png1.png 

imagedata           .. file: DOS 2.0 backup id file, sequence 48
b1,bgr,lsb,xy       .. text: "nootnoot$"
b2,r,lsb,xy         .. file: MacBinary, Mon Feb  6 06:28:16 2040 INVALID date, modified Mon Feb  6 06:28:16 2040 "PPPUP"
b2,b,msb,xy         .. text: "]UUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUU
~~~~~~~~~~~~~~
```
output2行目にhidden messageが表示されている。
Ans.: nootnoot$

#### 6.What about the payload used to encrypt it.
問題5の各行1列目に暗号化のpayloadが表示されている。
Ans.: b1,bgr,lsb,xy

---
### Task4 Exiftool
---
### Task5 Stegoveritas
---
### Task6 Spectrograms
---
### Task7 The Final Exam

