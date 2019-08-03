# copy files recursively under subdirectories, in Windows and linux

**Copy** to current path files searched with Perl regular 
expressions (regex) **recursively** under subdirectories,
in linux and Windows, with UTF-8 support.    
Copied files retain the directories from which they were copied,
separated with '**_**' chars.    
If folder name is too long it is abbreviated inserting '**...**' in the
middle and erasing chars to just 17 chars per directory.    
If a filename already exists in current path when copying, a number
(starting at 0001) will be appended to the copied filename, and so `cpr`
never will overwrite any file.   

Files can also be moved, instead of copied (with `-M`  parameter).   

## Use:

	cpr [-#hMnNpryY] [ '/FILTER_REGEX/' [PATH] ]

where '/FILTER_REGEX/' is a regular expression match pattern.
If '/FILTER_REGEX' is not indicated, '/./' is used (all files).
If PATH is not indicated, '.' is used (current path).

Please note that in linux simple quotation marks are preferred: ''
whilst in Windows double quotation marks are needed: ""

**-#**: a number indicating how many files at most will be copied
      from each subdirectory. For example: `-21`

**-h**: show full help with examples.

**-M**: MOVE files: they'll be removed from origin and moved to pwd.
      Note that, depending on `-p` and `-r`, files will be renamed.
      Default action (without `-M`) is COPY.

**-n**: when asked for confirmation a "NO" is automatically answered.   
**-N**: as `-n`, but output is also written to a log file in current path.

**-p**: preserve original filename. Without `-p` the path to each file
      is prepended to the filename.

**-r**: remove path passed as parameted from file prefixes.
      Note that `-p` makes `-r` redundant.

**-y**: when asked for confirmation a "YES" is automatically answered.   
**-Y**: as `-y`, but output is also written to a log file in current path.

The command shows a list of changes to be made, 
which must be confirmed with 'y' (yes).
If capital 'Y' is entered, the results are written to a log file.

Parameters can also be indicated using stdin.

The stdin input for parameters is needed in Windows in order to
use UTF-8 characters on regex, FILTER and/or PATH. Note that a cmd
console with UTF-8 support must be used. `CMD [/U]` isn't appropriate
in general, even with UTF-8 fonts (see below). In Windows, if stdin is used,
`-[yY]` would be needed to apply changes, because input cannot be read from the keyboard.

## Example of output

As you can see, the command shows a list of actions to execute, and requires previous confirmation:

	C:\temp> cpr "/LOG$/"

	Using:
	        path:                   '.'
	        search pattern:         /LOG$/

	.\subdir\file.20180702004423.LOG       ->      subdir_file.20180702004423.LOG
	.\subdir3\subdir3.4\file.LOG       ->      subdir3_subdir3.4_file.LOG

	--- (files =~ search pattern: 2)
	--- files to copy (listed): 2   (<=2)
	Do you want to copy here this file list? (y/ Y(=>log)/ *):


## Examples of use:

	$ cpr -1 '/\.jpg$/'

This copies one jpg image from each and every subdirectory under
the current path, to the current directory.

	$ cpr '/log$/' /home

This copies all files whose name end with 'log' from each and 
every subdirectory under '/home', to the current path.

	$ cpr -N '/report/i' /works

This creates a text file containing the path and filename 
of all files under '/works' containing the word 'report'
on their name. (The file also contains the filename they
would have in case they were copied to current path.)
With `-n` the results are just printed on screen.


## Windows installation and notes
In order to use the script in Windows you need:

* a Perl installation (for example [ActivePerl](https://www.activestate.com/activeperl) or [Strawberry Perl](http://strawberryperl.com/)). The script has been tested with ActivePerl.
* copy the `cpr` script and `cpr.bat` to a folder in your execution PATH

The `cpr.bat` batch script is provided as an example: it supposes that `perl.exe` is in the PATH, and `cpr` must be preceded by its complete path.

If your are planning to manage file names with UTF-8 chars (mostly outside Latin-1), a console with proprer UTF-8 I/O must be used. Windows' `CMD.EXE` isn't 100% adequate, even after installing UTF-8 fonts (See [serganov fonts](https://math.berkeley.edu/~serganov/ilyaz.org/software/fonts/) and [metacpan UI-KeyboardLayout](https://metacpan.org/pod/distribution/UI-KeyboardLayout/lib/UI/KeyboardLayout.pm#The-console-font-configuration)), because some visual artifacts remain. Instead, [ConEmu]( https://conemu.github.io/) is suitable.

Also note that in order to use most UTF-8 chars directly in the regex or filter parameters, the stdin with a `|` must be used.
Note also that in Windows the use of stdin for input parameters to cpr command requires the use of `-[yYnN]` modifiers, because unfortunately input cannot be redirected to the keyboard.
In Windows, to copy all files containing the japanese hiragana UTF-8 "の" char  under 'C:\temp2\':
(Note though that -[yY] is needed in order to actually take actions):

	C:\temp> echo  -y  "/の/"  "C:\temp2" |  cpr

*2018-Nov amendment*: In Windows `perl.exe` binary can be manually **patched and recompiled** in order to accept Unicode chars in command line: Please take a look at [Perl-with-Unicode-for-Windows](https://github.com/circulosmeos/Perl-with-Unicode-for-Windows).

## ListContent() subroutine

Subroutine *`ListContent()`* can be extracted from code and used indepently in other Windows or linux Perl projects. 
It just needs some global stuff, commented as "`# (...) ListContent() to run properly`" in the code.
Input parameters are `ListContent( $parameters_ref, $pattern, $path )` where:

* `$parameters_ref` is a reference to an array of boolean ( 0 | 1 ) numeric values representing, in order, the options:
`( $LS_STYLE, $TREAT_DIRECTORIES, $RECURSIVE, $NEGATED_MATCH, $MSDOS_SHORT_NAMES )`
* `$pattern` is a regex filter to apply to file names (or ls style pattern, even prepended with options (and even piped throu another command)). If `$NEGATED_MATCH` is `1`, is the regex filter that filenames do not comply with (`$LS_STYLE` must be zero, or otherwise `$NEGATED_MATCH` will be ignored!).
* `$path` is the filesystem path to be examinated

Return parameter is a reference to an array containing:

* `\@FILE_LIST`, a reference to an array of filenames (each path is in the same index on `@PATH_LIST` array)
* `\@PATH_LIST`, a reference to an array of paths corresponding to the filename of the same index on `@FILE_LIST` array
* number of directories returned, only to inform the number. Has sense only if `$TREAT_DIRECTORIES` call option was `1`
* `\@SHORT_FILENAME`, a reference to an array of path+filename in Windows' [MSDOS short name format](https://docs.microsoft.com/en-us/windows/desktop/fileio/naming-a-file#short-vs-long-names). Is not () only if OS is Windows and `$MSDOS_SHORT_NAMES` call parameter was `1` (or the number of returned files is `0`)


## General notes

* The difference between "files =~ search pattern" and "files to copy" numbers, if such, is due to the fact that files in current
path (not under any subdirectory) are initially counted, though they're later discarded as there is no sense in copying them to pwd... (they'd be overwritten by themselves). Future versions may stop counting files in current directory.


## author
Written by [circulosmeos](mailto:loopidle@gmail.com)
based on [`rnm`](https://github.com/circulosmeos/rnm)


## version
2018-11.04

## license
[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

## disclaimer
This code is provided "as is", without warranty of any kind, express or implied.
