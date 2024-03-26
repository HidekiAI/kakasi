# KAKASI

KAKASI は、 漢字かなまじり文をひらがな文やローマ字文に変換することを目的として 作成したプログラムと辞書の総称です。

"KAKASI" という名称は、"kanji kana simple inverter" の略です。 また、東北大学(現: 京都大学)の佐藤雅彦先生によって開発された SKK - simple kana kanji converter を逆から読んだものでもあります。 KAKASI の辞書のエントリのほとんどは SKK 辞書起源のものです。

KAKASI はフリーソフトウェアです。 あなたは、Free Software Foundation が公表した GNU General Public License (GNU 一般公有使用許諾) バージョン 2 あるいはそれ以降の各バージョンの中からいずれかを選択し、 そのバージョンが定める条項に従って本プログラムを 再頒布または変更することができます。

KAKASI は有用とは思いますが、頒布にあたっては、 市場性及び特定目的適合性についての暗黙の保証を含めて、 いかなる保証も行ないません。 詳細については GNU General Public License をお読みください。

KAKASI に関する情報は http://kakasi.namazu.org/ から得ることができます。

コメントやバグの報告は bug-kakasi@namazu.org 宛に、日本語か英語でお送り下さい。 注意: このメールアドレスは開発用のメーリングリストになっています。

- KAKASI project <kakasi-dev@namazu.org>
- Copyright (C) 1999-2001 KAKASI project. All rights reserved.

KAKASI is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 2, or (at your option) any later version.

KAKASI is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You can get information about KAKASI from http://kakasi.namazu.org/

Mail comments and bug reports to bug-kakasi@namazu.org in Japanese or English.


## LICENSE
The license (above) as stated on the following pages:

- http://kakasi.namazu.org/index.html.ja
- http://kakasi.namazu.org/index.html.en - there are no 

indicates that original source was/is of GPL v2 and later.

## Installations and Building

kakasi-2.3.6 source preserved from [kakasi-2.3.4.tar.gz](http://kakasi.namazu.org/stable/kakasi-2.3.4.tar.gz)

If you're just trying to build on Linux, either use your own distro package (i.e. even on latest Debian you *SHOULD* be able to do `$ apt install kakasi kakasi-dic libkakasi2` and get all working), so do NOT bother.  And even if you are in need to build/compile it by hand (i.e. perhaps you need to have an image on Docker?), you can just download the tar file from the original site (kakasi.namazu.org) and just `$ configure && make && sudo make install` and be done (it takes less than 5 minutes to compile and install this C project)!  IF you can handle the memory-hogging Windows WSL, then just install any of your favorite distro supporting WSL (for me, that would be Debian) and just `$ apt install kakasi kakasi-dic` and be done!

I've only created this repos because I have the need to build it on MinGW and unfortunately, neither MinGW `pacman` nor Cygwin have kakasi binary anymore, and I cannot handle the memory-hogging WSL to compete on RAM consumptions with other memory-hoggers (mainly static analysis language services such as `rust-analyzer` and `ionide` (F#) and `omnisharp` (C#) that commonly eats up gobs of RAM).

If and when the authors from namazu.org ever decides to create their own account to preserve kakasi, I will then make sure to put this repos in private mode.

## Minor modifications and alterations

I'd like to preserve the original as much as possible, but unfortunately the Windows version (at least on MinGW) does not link correctly due to the original archiving method using some tool called `lib` (which I've no clue what it is) no longer exists, so I've switched over to using `ar` archive tool to match Linux build.

- I altered the `configure` script to make it compile on MinGW
- Builds on both Linux and MinGW using `make` (see INSTALL file on how to build)
- Though it works on `make` (via autoconf) I would like to either update the `make` to output build artifacts outside `src` directory, or at least, wish to have it not pollute the original folders that the authors have written their source files to, so I am in the process of seeing if I can convert automake files to cmake on my spare time.

## Caveats

NOTE: The "kakasi.exe" found in the wild also has the following issues; there are other incomplete conversion of KAKASI (such as the rust kakasi crate) that only converts to either romaji or hiragana as well.

As for the current moment, though it compiles on both Linux and MinGW, when tested, Linux version works fine but MinGW version has issues with transforming back to UTF-8 even with the `-i utf8` flag set.  At first, I thought maybe the MinGW version of `iconv` is broken but this is what I get:

On Linux:

```bash
~/projects/github/kakasi/build$ ls . src
.:
aclocal.m4  ChangeLog     config.h     config.log    config.status  configure     COPYING  INSTALL
install-sh  kakasi-config     kakasidict   kakasi.spec.in  lib      ltmain.sh     maintMakefile
Makefile.am  man      NEWS   README     src       tests   TODO
AUTHORS     config.guess  config.h.in  config.rpath  config.sub     configure.in  doc
INSTALL-ja  itaijidict  kakasi-config.in  kakasi.spec  kanwadict       libtool  magic-kakasi
Makefile       Makefile.in  missing  ONEWS  README-ja  stamp-h1  THANKS

src:
78_83.c  a2.c  atoc_conv    atoc-conv.o  conv-util.h  dict.c  ee2.c  g2.c  getopt1.c  getopt.c
getopt.o  hh2.o     itaiji.o  j2.o   jj2.o  k2.o    kakasi.c  kakasi.o   kanjiio.o  kk2.o
level.h  Makefile     Makefile.in  mkkanwa.c  rdic_conv    rdic-conv.o  wx2-conv.c
78_83.o  a2.o  atoc-conv.c  conv-util.c  conv-util.o  dict.o  ee2.o  g2.o  getopt1.o  getopt.h
hh2.c     itaiji.c  j2.c      jj2.c  k2.c   kakasi  kakasi.h  kanjiio.c  kk2.c      level.c
level.o  Makefile.am  mkkanwa      mkkanwa.o  rdic-conv.c  wx2_conv     wx2-conv.o

~/projects/github/kakasi/build$ echo "最近人気の\nデスクトップな\nリナックスです!" | iconv -t UTF-8 -f UTF-8
最近人気の\nデスクトップな\nリナックスです!

~/projects/github/kakasi/build$ echo "最近人気の\nデスクトップな\nリナックスです!" | iconv -t UTF-8 -f UTF-8 | src/kakasi -f -i utf8 -o utf8 -JH
最近[さいきん]人気[にんき]の\nデスクトップな\nリナックスです!

$ locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME=C.UTF-8
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

On MinGW:

```bash
MINGW64 ~/projects/github/kakasi/build (main)
$ ls . src
.:
aclocal.m4  ChangeLog      config.h     config.log     config.status*  configure*    COPYING  INSTALL
install-sh*  kakasi.spec     kakasi-config*     kakasidict  lib/      ltmain.sh     maintMakefile
Makefile.am  man/      NEWS   README     src/      tests/  TODO
AUTHORS     config.guess*  config.h.in  config.rpath*  config.sub*     configure.in  doc/
INSTALL-ja  itaijidict   kakasi.spec.in  kakasi-config.in*  kanwadict   libtool*  magic-kakasi
Makefile       Makefile.in  missing*  ONEWS  README-ja  stamp-h1  THANKS

src:
78_83.c  a2.o            atoc-conv.o  conv-util.o  ee2.c  g2.o      getopt.o   hh2.c     itaiji.o
jj2.c  k2.o         kakasi.h   kanjiio.o  level.c  Makefile     mkkanwa.c     rdic_conv.exe*
wx2_conv.exe*
78_83.o  atoc_conv.exe*  conv-util.c  dict.c       ee2.o  getopt.c  getopt1.c  hh2.o     j2.c
jj2.o  kakasi.c     kakasi.o   kk2.c      level.h  Makefile.am  mkkanwa.exe*  rdic-conv.c   wx2-conv.c
a2.c     atoc-conv.c     conv-util.h  dict.o       g2.c   getopt.h  getopt1.o  itaiji.c  j2.o
k2.c   kakasi.exe*  kanjiio.c  kk2.o      level.o  Makefile.in  mkkanwa.o     rdic-conv.o     wx2-conv.o

MINGW64 ~/projects/github/kakasi/build (main)
$ echo "最近人気の\nデスクトップな\nリナックスです!" | iconv -t UTF-8 -f UTF-8
最近人気の\nデスクトップな\nリナックスです!

MINGW64 ~/projects/github/kakasi/build (main)
$ echo "最近人気の\nデスクトップな\nリナックスです!" | iconv -t UTF-8 -f UTF-8 | src/kakasi.exe -f -i utf8 -o utf8 -JH
µ£ÇΦ┐æ[πüòπüäπüìπéô]Σ║║µ░ù[πü½πéôπüì]πü«\nπâçπé╣πé»πâêπââπâùπü¬\nπâ¬πâèπââπé»πé╣πüºπüÖ!

MSYS ~/projects/github/kakasi/build
$ locale
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_ALL=
```

The usage of iconv here are redundant, but it's to prove that it's not iconv (on MinGW) that is the problem, nor is it MinGW (or Windows) itself, for it can handle UTF-8 based Japanese texts.

Another kind of test, to verify that kakasi is correctly interpreting kanji+hiragana+katakana, here are the proofs:

```bash
$ /mingw64/bin/kakasi -Ja -Ka -Ha  -i utf8 -o utf8  <<< "最近人気の\nデスクトップな\nリナックスです!"
saikinninkino\ndesukutoppuna\nrinakkusudesu!

# blending kanji to output:
$ /mingw64/bin/kakasi -Ja -Ka -Ha  -i utf8 -o utf8 -f ./kakasidict ./itaijidict <<< "最近人気の\nデスクトップな\nリナックスです!"
µ£ÇΦ┐æ[saikin]Σ║║µ░ù[ninki]no\ndesukutoppuna\nrinakkusudesu!
```

All in all, this tells me that it's actually (only) how kakasi no longer understands how to render as `-o utf8`

After tweaking here and there (only on MinGW side), it turns out the test on `iconv` to EUC-JP support (I think only on MinGW?  MinGW version of iconv is v1.17 while Debian is v2.36) is possibly broken, causing `configure` script to fail...  But to verify that, I've tried this:

```bash
MSYS ~/projects/lenzu
$ iconv -t EUC-JP -f UTF-8 <<< "最近人気の デスクトップな リナックスです!" > /tmp/iconv_out.txt ; iconv -f EUC-JP -t UTF-8 < /tmp/iconv_out.txt
最近人気の デスクトップな リナックスです!
```
And as you can see, it does properly convert from UTF-8 -> EUC-JP -> UTF-8...

In any case, for now, by removing the test on iconv in configure script, I'm now able to see something promising:

```bash
 MINGW64 ~/projects/github/kakasi
$ src/kakasi -JH -f -Ka -Ha  -i utf-8 -o utf-8 ./kakasidict ./itaijidict  <<< "最近人気の\nデスクトップな\nリナックスです!"
最近[さいきん]人気[にんき]no\ndesukutoppuna\nrinakkusudesu!

MINGW64 ~/projects/github/kakasi
$ src/kakasi -JH -f  -i utf-8 -o utf-8 ./kakasidict ./itaijidict  <<< "最近人気の\nデスクトップな\nリナックスです!"
最近[さいきん]人気[にんき]の\nデスクトップな\nリナックスです!

```

Just for the record, my build in MinGW had to do this (I'm using `clang64` as my choice to match my Debian):

```bash
MINGW64 ~/projects/github/kakasi
$ ./configure CC=/clang64/bin/clang.exe CFLAGS="-Wall -O2" LDFLAGS="-L/clang64/lib" LIBS="-liconv" CPPFLAGS="-I/clang64/include" CPP=/clang64/bin/clang-cpp.exe
```

I cannot push what I have, but if you want to build your own, look in my W.I.P. branch under `WIP/build/mingw` and grab the `configure` script (I think that's all you need).  Reason why I cannot push it are two reasons:

- It ONLY works when I open MSYS terminal, when I try it on other MinGW terminal (i.e. Git-for-Windows MSYS terminal), it outputs incorrectly.  `locale` command shows all to be same, so I've no clue what this is (if you know, please let me know)
- The output creates a "./.libs/kakasi.exe" on the same directory as "./kakasi.exe" which I've no clue what it is, but if the ".libs/kakasi.exe" directory and filename does not exist on same directory as "kakasi.exe", it will just output complete blank line.  Hence, `$ make install` will not work because it will not copy the `.libs/kakasi.exe` to target `/usr/bin` directory.  I've done quick research on what this `.libs/` artifacts are (note that neither `gcc` nor `clang` on Debian will create the artifacts, only on MinGW), but I'm not able to get a concrete answer (if you know what it is, please let me know, I need help getting `make install` to work again)

~ PEACE!
