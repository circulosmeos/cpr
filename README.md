# copy files recursively under subdirectories, in Windows and linux

**Copy** to current path files searched with Perl regular 
expressions (regex) **recursively** under subdirectories,
in linux and Windows, with UTF-8 support.    
Copied files retain the directories from which they were copied,
separated with '**_**' chars.    
If folder name is too long it is abreviated inserting '**...**' in the
middle and erasing chars to just 17 chars per directory.

## Use:

	cpr [-#hnNpryY] [ '/FILTER_REGEX/' [PATH] ]

where '/FILTER_REGEX/' is a regular expression match pattern.
If '/FILTER_REGEX' is not indicated, '/./' is used (all files).
If PATH is not indicated, '.' is used (current path).

Please note that in linux simple quotation marks are preferred: ''
whilst in Windows double quotation marks are needed: ""

**-#**: a number indicating how many files at most will be copied
      from each subdirectory. For example: `-21`

**-h**: show full help with examples.

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

## author
Written by [circulosmeos](mailto:loopidle@gmail.com)
based on [`rnm`](https://github.com/circulosmeos/rnm)

## version
2018-09.15

## license
[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

## disclaimer
This code is provided "as is", without warranty of any kind, express or implied.