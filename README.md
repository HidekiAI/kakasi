# kakasi
kakasi-2.3.6 source preserved from http://kakasi.namazu.org/stable/kakasi-2.3.6.tar.gz

The license as stated on the following pages:

- http://kakasi.namazu.org/index.html.ja
- http://kakasi.namazu.org/index.html.en

indicates that original source was/is of GPL v2 and later.

## Other modification

I'd like to preserve the original as much as possible, but unfortunately the Windows version (at least on MinGW) does not build

- Builds on both Linux and MinGW using `make` (see INSTALL file on how to build)

## Alterations

- Though it works on `make` (via autoconf) I would like to either update the `make` to output build artifacts outside `src` directory, or at least, wish to have it not pollute the original folders that the authors have written their source files to, so I am in the process of seeing if I can convert automake files to cmake on my spair time.
- I altered the `configure` script to make it compile on MinGW

## Caveats

As for the current moment, though it compiles on both Linux and MinGW, when tested, Linux version works fine but MinGW version has issues with transforming back to UTF-8 even with the `-i utf8` flag set.  At first, I thought maybe the MinGW version of `iconv` is broken but this is what I get:

On Linux:

```bash
~/projects/github/kakasi/build$ ls . src
.:
aclocal.m4  ChangeLog     config.h     config.log    config.status  configure     COPYING  INSTALL     install-sh  kakasi-config     kakasidict   kakasi.spec.in  lib      ltmain.sh     maintMakefile  Makefile.am  man      NEWS   README     src       tests   TODO
AUTHORS     config.guess  config.h.in  config.rpath  config.sub     configure.in  doc      INSTALL-ja  itaijidict  kakasi-config.in  kakasi.spec  kanwadict       libtool  magic-kakasi  Makefile       Makefile.in  missing  ONEWS  README-ja  stamp-h1  THANKS

src:
78_83.c  a2.c  atoc_conv    atoc-conv.o  conv-util.h  dict.c  ee2.c  g2.c  getopt1.c  getopt.c  getopt.o  hh2.o     itaiji.o  j2.o   jj2.o  k2.o    kakasi.c  kakasi.o   kanjiio.o  kk2.o    level.h  Makefile     Makefile.in  mkkanwa.c  rdic_conv    rdic-conv.o  wx2-conv.c
78_83.o  a2.o  atoc-conv.c  conv-util.c  conv-util.o  dict.o  ee2.o  g2.o  getopt1.o  getopt.h  hh2.c     itaiji.c  j2.c      jj2.c  k2.c   kakasi  kakasi.h  kanjiio.c  kk2.c      level.c  level.o  Makefile.am  mkkanwa      mkkanwa.o  rdic-conv.c  wx2_conv     wx2-conv.o

~/projects/github/kakasi/build$ echo "最近人気の\nデスクトップな\nリナックスです!" | iconv -t UTF-8 -f UTF-8
最近人気の\nデスクトップな\nリナックスです!

~/projects/github/kakasi/build$ echo "最近人気の\nデスクトップな\nリナックスです!" | iconv -t UTF-8 -f UTF-8 | src/kakasi -f -i utf8 -o utf8 -JH
最近[さいきん]人気[にんき]の\nデスクトップな\nリナックスです!
```

On MinGW:

```bash
MINGW64 ~/projects/github/kakasi/build (main)
$ ls . src
.:
aclocal.m4  ChangeLog      config.h     config.log     config.status*  configure*    COPYING  INSTALL     install-sh*  kakasi.spec     kakasi-config*     kakasidict  lib/      ltmain.sh     maintMakefile  Makefile.am  man/      NEWS   README     src/      tests/  TODO
AUTHORS     config.guess*  config.h.in  config.rpath*  config.sub*     configure.in  doc/     INSTALL-ja  itaijidict   kakasi.spec.in  kakasi-config.in*  kanwadict   libtool*  magic-kakasi  Makefile       Makefile.in  missing*  ONEWS  README-ja  stamp-h1  THANKS

src:
78_83.c  a2.o            atoc-conv.o  conv-util.o  ee2.c  g2.o      getopt.o   hh2.c     itaiji.o  jj2.c  k2.o         kakasi.h   kanjiio.o  level.c  Makefile     mkkanwa.c     rdic_conv.exe*  wx2_conv.exe*
78_83.o  atoc_conv.exe*  conv-util.c  dict.c       ee2.o  getopt.c  getopt1.c  hh2.o     j2.c      jj2.o  kakasi.c     kakasi.o   kk2.c      level.h  Makefile.am  mkkanwa.exe*  rdic-conv.c     wx2-conv.c
a2.c     atoc-conv.c     conv-util.h  dict.o       g2.c   getopt.h  getopt1.o  itaiji.c  j2.o      k2.c   kakasi.exe*  kanjiio.c  kk2.o      level.o  Makefile.in  mkkanwa.o     rdic-conv.o     wx2-conv.o

MINGW64 ~/projects/github/kakasi/build (main)
$ echo "最近人気の\nデスクトップな\nリナックスです!" | iconv -t UTF-8 -f UTF-8
最近人気の\nデスクトップな\nリナックスです!

MINGW64 ~/projects/github/kakasi/build (main)
$ echo "最近人気の\nデスクトップな\nリナックスです!" | iconv -t UTF-8 -f UTF-8 | src/kakasi.exe -f -i utf8 -o utf8 -JH
µ£ÇΦ┐æ[πüòπüäπüìπéô]Σ║║µ░ù[πü½πéôπüì]πü«\nπâçπé╣πé»πâêπââπâùπü¬\nπâ¬πâèπââπé»πé╣πüºπüÖ!
```

The usage of iconv here are redundant, but it's to prove that it's not iconv (on MinGW) that is the problem, nor is it MinGW (or Windows) itself, for it can handle UTF-8 based Japanese texts.
