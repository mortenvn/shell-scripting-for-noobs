# ----------------------------------------------------------------------
# | Write to screen                                                    |
# ----------------------------------------------------------------------
echo "Hello world!"

# Though, printf is supposed to be better than echo (http://unix.stackexchange.com/questions/65803/why-is-printf-better-than-echo)
$ printf "some\nmulti-lined\ntext\n"
some
multi-lined
text

$ echo "some\nmulti-lined\ntext"
some\nmulti-lined\ntext

$ echo -e "some\nmulti-lined\ntext"
some
multi-lined
text

# Also, with printf you can print in color!
red='\e[1;31m%s\e[0m\n'
green='\e[1;32m%s\e[0m\n'
yellow='\e[1;33m%s\e[0m\n'
blue='\e[1;34m%s\e[0m\n'
magenta='\e[1;35m%s\e[0m\n'
cyan='\e[1;36m%s\e[0m\n'

printf "$green"   "This is a test in green"
printf "$red"     "This is a test in red"
printf "$yellow"  "This is a test in yellow"
printf "$blue"    "This is a test in blue"
printf "$magenta" "This is a test in magenta"
printf "$cyan"    "This is a test in cyan"


# ----------------------------------------------------------------------
# | Variables                                                          |
# ----------------------------------------------------------------------

# New variable
$ hi="Hello world!"
$ echo $hi # notice the $
Hello world!

# Read-only variables (its seen as best practice to use uppercase)
$ declare -r GITHUB='mortenvn/dotfiles'
$ GITHUB='herpaderp/dotfiles' # will cause error

# local variables (very useful in functions!)
local url="$1"

# Destroy variable
$ unset variable_name

# ----------------------------------------------------------------------
# | Arrays                                                             |
# ----------------------------------------------------------------------
# http://wiki.bash-hackers.org/syntax/array
# http://www.thegeekstuff.com/2010/06/bash-array-tutorial/

# Declaring an array
declare -a name

# Initializing
array=(one two three)
# OR
array[0]=one
array[1]=two
array[2]=three

# Getting the values (dereferencing the variables)
# In order to refer to the content of an item in an array, use curly braces:
# http://www.thegeekstuff.com/2010/06/bash-array-tutorial/
echo ${ARRAY[*]}
one two three
# OR
echo ${ARRAY[2]}
three

# Length of the array using #
$ declare -a Unix=('Debian' 'Red hat' 'Suse' 'Fedora');
$ echo ${#Unix[@]} # Number of elements in the array (* and @ does the same thing)
4
$ echo ${#Unix[0]}  # Number of characters in the first element of the array.i.e 'Debian'
6
$ echo ${#Unix} # Same as ${#Unix[0]}
6

# Search and replace in arrays
$ Unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');
$ echo ${Unix[@]/Ubuntu/TheChosenOne}
Debian Red hat TheChosenOne Suse Fedora UTS OpenLinux

# Appending to existing array
Unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');
Unix=("${Unix[@]}" "AIX" "HP-UX")

# ----------------------------------------------------------------------
# | parameter expansion (difference between $var and ${var})           |
# ----------------------------------------------------------------------
# http://wiki.bash-hackers.org/syntax/pe#simple_usage
# http://stackoverflow.com/questions/2188199/how-to-use-double-or-single-bracket-parentheses-curly-braces
# http://stackoverflow.com/questions/8748831/bash-when-do-we-need-curly-braces-in-variables
# In most cases it makes no difference. But sometimes it is nifty or required:

# {} is useful if you want to expand the variable foo in the string "${foo}bar"
# since "$foobar" would instead expand foobar.
"${foo}bar"

# When expanding arrays
${array[42]}

# Make substitutions similar to sed
$ var="abcde"; echo ${var/de/12}
abc12

# When expanding positional parameters beyond 9:
"$8 $9 ${10} ${11}"

# ----------------------------------------------------------------------
# | Executing commands with $(command) and `command`                    |
# ----------------------------------------------------------------------
# $(...) (or its alias ``) allows command substitution, i.e. allows the
# output of a command # to replace the command itself.
$ echo pwd
pwd

$ echo $(pwd)
/Users/mortnod/Resources/new-dotfiles

# ----------------------------------------------------------------------
# | Functions                                                          |
# ----------------------------------------------------------------------

# New function
function_name () {
  # Insert magic here
}

# Calling your function
function_name

# You can access parameters used with $1, $2, $3 and so on
foo 1  # this will fail because foo has not been declared yet.

foo() {
    echo "Parameter #1 is $1"
}

foo 2 # this will work.

# ----------------------------------------------------------------------
# | Exit values                                                        |
# ----------------------------------------------------------------------

# $? returns the exit value of the last executed command
# 0 == success, 1 == failure
$ ls
$ $?
0 # 0 meaning everything went well

$ ls hasdhjklashdjklahs
$ $?
1 # 1 meaning an error occurred

# ----------------------------------------------------------------------
# | Concatenating commands with || && ;                                |
# ----------------------------------------------------------------------
# && lets you do something based on whether the previous command completed successfully.
# That's why you tend to see it chained as do_something && do_something_else_that_depended_on_something.

# Examples
$ false || echo "Oops, fail"
Oops fail

$ true || echo "Will not be printed"
$

$ true && echo "Things went well"
Things went well

$ false ; echo "This will always run"
This will always run


# ----------------------------------------------------------------------
# | source & sh                                                        |
# ----------------------------------------------------------------------

# When you call source (or its alias .), you insert the script in the current
# bash process. So you could read variables set by the script.
$ source cool_functions_and_variables.sh
$ echo $cool_variable_from_the_file
Soo cool!

# When you call sh, you initiate a fork (sub-process) that runs a new session of /bin/sh,
# which is usually a symbolic link to bash. In this case, environment variables set by
# the sub-script would be dropped when the sub-script finishes.
sh cool_functions_and_variables.sh
echo $cool_variable_from_the_file # Will create error since no such variable exists in the current shell

# NB! ./ and source are not quite the same.
# `./script` runs the script as an executable file, launching a new shell to run it
# `source script` reads and executes commands from filename in the current shell environment
# Note: ./script is not . script, but . script == source script


# ----------------------------------------------------------------------
# | If statements                                                      |
# ----------------------------------------------------------------------

# Syntax
count=99
if [ $count -eq 100 ]; then
  echo "Count is 100"
elif [ $count -gt 100 ]; then
  echo "Count is greater than 100"
else
  echo "Count is less than 100"
fi

# Test commands (http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html)
[ -e FILEPATH ] # True if file exists.
[ -r FILEPATH ] # True if file exists and is readable.
[ -w FILEPATH ]	# True if file exists and is writable.
[ -x FILEPATH ]	# True if file exists and is executable.

[ ARG1 -eq ARG2 ]

[ ! EXP ] # True if EXP is false.
[ EXP1 -a EXP2 ] # True if both the expression is true.
[ EXP1 -o EXP2 ] # True if either of the expression1 or 2 is true.

[ -n STRING ] or [ STRING ]	# True if the length of "STRING" is non-zero.
[ "$OS_NAME" == "Darwin" ] # True if the strings are equal.
[ STRING1 != STRING2 ] # True if the strings are not equal.

# What's the difference between [ and [[? http://stackoverflow.com/questions/669452/is-preferable-over-in-bash-scripts

# You don't ALWAYS have to use [] for the test. You can use boolean logic
if command; then ...
# too, because the commands have exit status. In bash, 0 is true and > 0 is false.

# ----------------------------------------------------------------------
# | Brace expansions {}                                                |
# ----------------------------------------------------------------------
# Brace expansion is a mechanism by which arbitrary strings can be generated.
$ echo a{d,c,b}e
ade ace abe

# This construct is most useful when the strings involved have a long common prefix, such as a long file path.
$ mv app/{alpha,beta}.py
# becomes
$ mv app/alpha.py app/beta.py


# ----------------------------------------------------------------------
# | I/O streams: stdin, stdout & stderr                                |
# ----------------------------------------------------------------------
# There are three standard I/O streams:
# Standard input: This is the stream that your process reads to get information from you.
# Standard output: Your process writes normal information to this stream
# Standard error: Your process writes error information here.

# If I tell the python shell to do a print:
print 'what is your name?'
# "what is your name" will go to stdout

# ----------------------------------------------------------------------
# | Redirection of I/O streams                                         |
# ----------------------------------------------------------------------

# > means redirection. > mean send to as a whole completed file, overwriting target if exist
# >> means *send in addition*. Will append to target if exist.

# Output to file
echo "hey" > afile.txt
#redirects stdout to afile.txt. This is the same as doing..
echo "hey" 1> afile.txt

# Error to file
grep da * 2> grep-errors.txt

# Redirect to another stream
# You can use the >& operator to redirect from one stream to another
ls 2>&1 # redirects STDERR into STDOUT
ls 1>&2 # redirects STDOUT into STDERR

# Deleting Output
# /dev/null is a special filesystem object that throws away everything written
# into it. Redirecting a stream into it means hiding an output.

ls > /dev/null # Hides output
ls 2> /dev/null # Hide errors

ls > /dev/null 2>&1 # Hides both output and errors
&> /dev/null # This is just an abbreviation for > /dev/null 2>&1.


# ----------------------------------------------------------------------
# | Pipes                                                              |
# ----------------------------------------------------------------------

# To connect the STDOUT of one command to the STDIN of another use the | symbol,
# commonly known as a pipe.

thing1 | thing2
# does the same thing as is the same as...
thing1 > temp_file && thing2 < temp_file

# Difference between Pipe and redirects:
# Pipe is used to send the output to other command whereas to redirect is
# used to redirect the output to some file.


Check if a command exists


# ----------------------------------------------------------------------
# | Difference between (), (()), [], [[]]                                                              |
# ----------------------------------------------------------------------

# Single Parenthesis - ( ... ) is creating a subshell
# Double Parenthesis - (( ... )) is for arithmetic operation
# Single Square Bracket - [ ... ] is the syntax for the POSIX test
# Double Square Brackets - [[ ... ]] is the syntax for bash conditional expressions (similar to test but more powerful)
