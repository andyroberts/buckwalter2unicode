buckwalter2unicode.py - A script to convert transliterated Arabic
                        (using the Buckwalter system) to Unicode.

Version 0.2 - 15th September 2004

Andrew Roberts - http://www.andy-roberts.net

This program is licensed under the General Public License (see bottom of file.)

About
=====

This buckwalter2unicode script is designed to convert Arabic text that
has been transliterated to ASCII symbols using the Buckwalter
Transliterion method. (See http://www.qamus.org/transliteration.htm for
full details of his approach). The output is Arabic text in the Unicode
encoding.

There is also the option to perform the transliteration is reverse, that
is, taking a Unicode file containing Arabic, and converting to ASCII
symbols.

Setting up
==========

buckwalter2unicode is programmed in Python. Python is an interpreted
language, and therefore you will need the Python software installed on
your system before you can run this script. This program was written and
tested on Python 2.2.2 only. I am pretty sure that due to Unicode
issues, it will not run on any version less than 2.2. At the time of
writing Python 2.3.4 is available.

Python is available for most major platform, including Microsft Windows,
Linux (and other UNIX platforms), Mac OS X, etc. Look to
http://www.python.org for details on how to download and install Python
on your system.

Other than Python, there are no other dependencies.

How to use
==========

Open a command-prompt. Move to the directory where the script has been
saved. Then type: 

 python buckwalter2unicode.py -h

This will invoke an instance of Python, which will then execute the code
in the buckwalter2unicode.py file. The `-h' argument tells the script
to output the possible options for using this program. It will look like
this:


Usage: buckwalter2unicode.py -i INFILE -o OUTFILE [-g CHARS -r -e INPUT_ENCODING, -E OUTPUT ENCODING]
       buckwalter2unicode.py -l
       buckwalter2unicode.py -h

  -i, --input:
    Path to text file to be transliterated to Unicode.

  -o, --output:
    Path of file to output the newly transliterated text.

  -e, --input-encoding:
    Specify the text encoding of the source file. Default: latin_1.

  -E, --output-encoding:
    Specify the text encoding of the target file. Default: utf_8.

  -g CHARS, --ignore-lines=CHARS:
    Will not transliterate lines that start with any of the CHARS
    given. E.g., -g #; will not alter lines starting with # or ;.
    (May need to be -g \#\; on some platforms. See README.txt.

  -c RANGE, --columns=RANGE:
    If in columns, select columns to apply transliteration. Can be
    comma separated numbers, or a range. E.g., -c 1, -c 1-3, -c 1,3.

  -d CHAR, --delimiter=CHAR:
    Specify the delimiter that defines the column if using the -c
    option above. Default is ' ' (space).

  -r, --reverse:
    Reverses the transliteration, i.e., Arabic to Buckwalter.
    When used, it will change the default input encoding to utf_8 and
    output encoding to latin_1

  -l, --list-encodings:
    Displays all supported file encodings.

  -h, --help:
    Displays this page.

Examples:

You have a file called arabic.txt that contains Arabic that has been
written as ASCII symbols under the Buckwalter system. Using:

 $ python buckwalter2unicode.py -i arabic.txt -o arabic_utf8.txt

Will convert the input to proper Arabic using the Unicode UTF-8
encoding. 

If you know actual encoding of the input file, then you can specify
using the `-e' flag. E.g., if arabic.txt was written on a European
version of Windows, then it is likely to be encoding with Windows
CodePage 1252. To use this information:

 $ python buckwalter2unicode.py -i arabic.txt -e windows-1252 -o arabic_utf8.txt

If you would like to use a different Unicode standard, such as UTF-16
for the output, then this can be specified with the `-E' flag as
follows:

 $ python buckwalter2unicode.py -i arabic.txt -e windows-1252 -o arabic_utf8.txt -E utf_16

Specifying encodings is optional. The default encodings are latin_1
(also known as ISO-8859-1) for input, and UTF-8 for output.

If you want to perform Unicode -> Buckwalter transliteration, you must
supply the `-r' flag. You still need to specify the input and output
filenames, and any encoding information if appropriate. Note, because of
the reverse direction, then the encoding defaults are also swapped. The
input file is assumed to be a Unicode file, therefore UTF-8 is the
default. For output, as it's converting to ASCII, the latin_1 encoding
will be used.

The `-l' option can be used that will simply print to screen all the
available encodings that Python currently supports. The list is quite
long. This list will show the `codec' name for each encoding. This is
basically the name Python uses to refer to encoding names. There is then
a column of aliases, as often there are slight permutations of the name,
e.g., `utf_16' is a codec, and `utf16' and `U16' are aliases. A final
column gives a brief description of the language(s) supported by that
particular encoding.

Ignoring certain lines
======================

The -g option allows you to specify a string of letters/symbols. If any
of the lines in the input file begin with any of the characters given,
then no transliteration will be applied to that line, and it will be
output verbatim. This is useful for files that lines that contain
comments about subsequent data, as the transliteration would most likely
turn them into meaningless strings. Often these lines are annotated with
a symbol to indicate that they are comments.

E.g., -g #$ 

This translates to lines beginning with either # or $ will not be
transliterated. It does NOT mean lines beginning with #$. Unfortunately,
multi-character conditions are not supported. (Yet!)

NOTE: Depending on your platform and command-prompt shell, certain symbols
have special meanings to that shell. For example, I use the Linux platform
with the Bash shell. The # symbol has a special meaning with this shell. In
this event you will have to `escape' the symbol. This is done by prefixing
your chosen symbol with a backslash. If I want to get the above example
working correctly, I need to use the following:

 -g \#$

Typically, you are aware when you need to use escaped symbols. If when you
enter the command with literal symbols and they have special significance, the
program will abort and you will see the usage information of the program.

I have not been able to test this on all platforms, however, for my setup with
Bash on Linux, I know that the following set of symbols need to be escaped
before they will work correctly:
`
#
"
&
(
)
;
'
|
<
>
\

If you need to use any of the above symbols, then simply prefix with a
backslash (`\').

Transliterating certain columns only
====================================

Imagine you have a file that looks as follows:

ktb katab write
ktb kitAb book
mktb makotab library
...

The format is:

unvowelled_word vowelled_word english_translation

Three columns, each separated (delimited) by a space.

If you were to pass this through the transliteration system, then by default,
it would convert the entire line, including the English words in the final
column. The output would look fine for the first two columns, but you can
imagine that the translation will look unrecognisible.

It is possible to specify which columns to transliterate if you wish to be
specific. (There is of course the requirement that there must be something that
will consistantly allow the code to identify the column boundaries. In this
instance, all columns are delimitted by a space.)

if you add the -c RANGE option at the command line, you can tell the program
which columns are to be transliterated. The RANGE can be described as a comma
separated sequence of column numbers (`1,2'), a column range (`1-2') or a
combination of both (`1,2,3-5,9'). Note, 1 equals the first column and so on.

Therefore, for the above example, we'd want:

 -c 1-2

Setting the delimeter
=====================

In the above previous example, the script seemed to `know' where the column
boundaries were. This was actually coincidence: the default delimiter is a
space character (' '), which just happened to be the same as the delimiter of
the example. Take the similar example:

ktb;katab;write
ktb;kitAb;book
mktb;makotab;library

The only difference here is that the delimiter is a semi-colon (;) rather than
a space. However, the program's default is a space. To override this default,
use the -d option as follows:

 -c 1-2 -d \;

As mentioned earlier, using the semi-colon at the command-line may require you
to escape using the backslash. 

This option only accepts single character delimiters. If you were to input
more than one character, the script will automatically strip away additional
ones. 

Note: do not try to use a symbol that also appears in the Buckwalter
tranliteration character set. It will obivously cause huge problems. The
program will abort if you try this.

FYI: The space character is actually not the best choice of delimiters for
textual data, as sometimes spaces may be part of a phrase that is not meant to
be confused with a column separator. For example, take the following similar
example:

kAtb kAtab correspond with ktb
ktAb kitAb piece of writing ktb

The format is slightly different, in that there is an additional column that
contains the root of the preceeding Arabic words. However, you will notice
that the translations happen to also contain spaces. This now presents a
problem, in that for the first line, columns 1,2,5 are the ones to be
transliterated, yet for the second line it is 1,2,6! It is no longer possible
to specify a consistant range. If an alternative delimiter was used within the
source file itself, it would be easy, e.g.,

kAtb	kAtab	correspond with	ktb
ktAb	kitAb	piece of writing	ktb

The above example now uses tabs. Tabs are typically represented as \t. But to
use on the command-line you will need a double backslash:

 -d \\t

A note about encodings
======================


Some people may be sligtly bewildered about the regular mentions of
`encodings' in this file. For those who are unsure of what I mean, I
recently saw it well explained by Joel Spolsky on his website. Joel is a
software engineer, and despite the title of his article being "The
Absolute Minimum Every Software Developer Absolutely, Positively Must
Know About Unicode and Character Sets (No Excuses!)", I believe that you
don't have to be a programmer to understand it. You can find this
article at: http://www.joelonsoftware.com/articles/Unicode.html

Support
=======

This is a relatively simple program, therefore no manual (other than
this README) has been written. If you have any problems, spot and bugs
and want to pass on any comments/feedback please feel free to contact me
via email. Contact details are available via http://www.andy-roberts.net

License information
===================

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA
