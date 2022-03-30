# CC:Steganography

## About the CTF
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

- 1.What argument allows you to embed data(such as files) into other files?  `embed`
- 2.What flag let's you set the file to embed?  `-ef`
- 3.What flag allows you to set the "cover file"?(i.e  the jpg)  `-cf`
- 4.How do you set the password to use for the cover file?  `-p`
- 5.What argument allows you to extract data from files?  `extract`
- 6.How do you select the file that you want to extract data from?  `-sf`
- 7.Given the passphrase "password123", what is the hidden message in the included "jpeg1" file.
以下のコマンドを実行する。
```
steghide extract -sf jpeg1.jpeg
```
passwordの入力を求められるので、`password123`を入力。
出力されたファイル（a.txt）の内容を確認
```
cat a.txt
```
`pinguftw`

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

- 1.How do you specify that the least significant bit comes first  `--lsb`
- 2.What about the most significant bit?  `--msb`
- 3.How do you specify verbose mode?  `-v`
- 4.How do you extract the data from a specific payload?  `-E`
- 5.In the included file "png1" what is the hidden message?
zstegのコマンドを実行し、hidden messageを確認する。
```
# zsteg png1.png 

imagedata           .. file: DOS 2.0 backup id file, sequence 48
b1,bgr,lsb,xy       .. text: "nootnoot$"
b2,r,lsb,xy         .. file: MacBinary, Mon Feb  6 06:28:16 2040 INVALID date, modified Mon Feb  6 06:28:16 2040 "PPPUP"
b2,b,msb,xy         .. text: "]UUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUU
~~~~~~~~~~~~~~
```
コマンド結果の2行目にhidden messageが表示されている。
`nootnoot$`

- 6.What about the payload used to encrypt it.
問題5の各行1列目に暗号化のpayloadが表示されている。
`b1,bgr,lsb,xy`

---
### Task4 Exiftool
- 1.In the included jpeg3 file, what is the document name
exiftoolを使用してjpeg3ファイルのExif情報を確認する。
```
└─# exiftool jpeg3.jpeg 
ExifTool Version Number         : 12.40
File Name                       : jpeg3.jpeg
Directory                       : .
File Size                       : 8.3 KiB
File Modification Date/Time     : 2020:01:06 21:09:44+00:00
File Access Date/Time           : 2020:01:06 21:09:46+00:00
File Inode Change Date/Time     : 2022:03:29 12:27:26+00:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Exif Byte Order                 : Big-endian (Motorola, MM)
Document Name                   : Hello :)
X Resolution                    : 1
Y Resolution                    : 1
Resolution Unit                 : None
Y Cb Cr Positioning             : Centered
Image Width                     : 213
Image Height                    : 160
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 213x160
Megapixels                      : 0.034
```
`Document Name`に答えが出ている。
`Hello :)`

---
### Task5 Stegoveritas
`stegoveritas`のhelpを参照する。
```
# stegoveritas -h
usage: stegoveritas [-h] [-out dir] [-debug] [-password PASSWORD] [-wordlist WORDLIST] [-meta] [-imageTransform] [-bruteLSB] [-colorMap [N ...]] [-colorMapRange Start End] [-extractLSB]
                    [-red index [index ...]] [-green index [index ...]] [-blue index [index ...]] [-alpha index [index ...]] [-extract_frames] [-trailing] [-steghide] [-exif] [-xmp] [-carve]
                    file

Yet another Stego tool

positional arguments:
  file                  The file to analyze

optional arguments:
  -h, --help            show this help message and exit
  -out dir              Directory to place output in. Defaults to ./results
  -debug                Enable debugging logging.
  -password PASSWORD    When applicable, attempt to use this password to extract data.
  -wordlist WORDLIST    When applicable, attempt to brute force with this wordlist.

image options:
  -meta                 Check file for metadata information
  -imageTransform       Perform various image transformations on the input image and save them to the output directory
  -bruteLSB             Attempt to brute force any LSB related steganography.
  -colorMap [N ...]     Analyze a color map. Optional arguments are colormap indexes to save while searching
  -colorMapRange Start End
                        Analyze a color map. Same as colorMap but implies a range of colorMap values to keep
  -extractLSB           Extract a specific LSB RGB from the image. Use with -red, -green, -blue, and -alpha
  -red index [index ...]
  -green index [index ...]
  -blue index [index ...]
  -alpha index [index ...]
  -extract_frames       Split up an animated gif into individual frames.
  -trailing             Check for trailing data on the given file
  -steghide             Check for StegHide hidden info.

multi options:
  -exif                 Check this file for exif information.
  -xmp                  Check this file for XMP information.
  -carve                Attempt to carve/extract things from this file.

Have a good example? Wish it did something more? Submit a ticket: https://github.com/bannsec/stegoVeritas
```
- 1.How do you check the file for metadata?  `-meta`
- 2.How do you check for steghide hidden information  `-steghide`
- 3.What flag allows you to extract LSB data from the image?  `-extractLSB`
- 4.In the included image jpeg2 what is the hidden message?

`stegoveroveritas`のコマンドを実行する。
```
# stegoveritas -steghide jpeg2.jpeg 

Running Module: SVImage
+------------------+------+
|   Image Format   | Mode |
+------------------+------+
| JPEG (ISO 10918) | RGB  |
+------------------+------+
Found something with StegHide: /root/spect/results/steghide_45278306f5b457f1ab019d90d55e94c9.bin
Running Module: MultiHandler
```
結果が`result`のディレクトリに出力されているので確認する。
```
cat ./results/steghide_45278306f5b457f1ab019d90d55e94c9.bin

kekekekek
```

`kekekekek`

---
### Task6 Spectrograms
- 1.What is the hidden text in the included wav2 file?
Sonic Visualizerを起動し、`wav2.wav`を読み込む。
Layer > Add Spectrogramを選択する。

<img width="957" alt="Sonic_Visualiser" src="https://user-images.githubusercontent.com/67187325/160840097-05aa1916-ca59-473a-875d-f4f5bdf77f48.png">

`Google`
---
### Task7 The Final Exam

