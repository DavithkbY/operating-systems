# Tasks

## Introduction
The purpose of this assignment is to get familiar with operating a Linux system from the command line.

To get you started, you can have a look at:

* [Introduction to Linux – A Hands on Guide, M. Garrels](http://www.tldp.org/LDP/intro-linux/)
* Command line cheat sheet

## The shell
Everything in Linux is perfectly manageable in a text based environment. Lots of tasks are even simpler and faster to accomplish from a command line interface or text based environment. Because graphical environments waste resources and could cause extra security problems, Linux system administrators often choose only to install a text based environment.

## Finding help
Before you begin it is useful to mention some places to find help when working with the command line. In the first place, you can check the documentation on the system itself. 

There is a *manual page* for almost every command available. To access this manual page type `man <command>`. You can read the page and leave it by pressing `q`.

Try this for the commands `pwd` and `ls`. Thanks to man you don’t have to learn every option of every command, you just can look them up.

For a short description of a commands function you can make use of whatis.

```bash
$ whatis ls
ls (1)               - list directory contents
```

If you don’t know the name of a command, you can look for it with `apropos`. The man pages will be searched for your query. For example when you can't remember how to copy files, apropos can help you out:


```
$ apropos 'copy files'
cp (1)               - copy files and directories
cpio (1)             - copy files to and from archives
install (1)          - copy files and set attributes
```
Now you know you can use the command `cp` and with `man cp` you will find all the options you need.

## Files and directories
The shell welcomes you with a prompt which you can adapt to your personal wishes. By default it looks something like this: 

```
bob@system:/etc/init.d$
```

 This tells you that user 'bob' has logged in on machine ‘system’ and is currently working in the directory '/etc/init.d'.

To ask the system for the current location in the filesystem hierarchy use the `pwd` command.

Just like in Windows, Linux uses a directory hierarchy. It starts at the root (/). What follows are some main directories and their contents:

```
/root			Root directory: structural base
/home			Contains a homedirectory per user
/etc			Contains configuration files for the system
/bin			Contains executable files
/lost+found		Contains data found when maintaining the file system.
.				Current directory
..				Directory one level up (parent)
~				Home directory of logged in user
```

Navigation between directories happens with the `cd` command. To go to the /bin directory you type `cd /bin`. In a directory you can find subdirectories and files. You ask for a directory’s content with the `ls` command.

```
$ cd /bin
$ ls 
arch dnsdomainname login pidof 
... tempfile
```

You will get a list with names of files and directories in the current working directory. Alternatively, you can ask for the listing of the directory by specifying it: `ls /home` will print the home directory's contents. 

To get more information about the files you can add some options to `ls`. For example:

```
$ ls -l
total 3816
rwxrxrx 1 root root 2816   Sep 24 00:34 arch
rwxrxrx 1 root root 110872 Apr 27 2003  ash
...
```

To create a directory you use the command `mkdir`. To delete an empty directory you can use `rmdir`.


## Permissions
On Linux every file and every directory has rights associated with it. You can use the command ls -l to view them.

```
bob@system:~/permissions$ ls -lh
total 16K
-rw-rw----  1 bob ldapusers 1.5K 2005-02-14 00:43 group.txt
-rwxr-xr--  1 bob ldapusers 3.6K 2005-02-14 00:43 everyone.txt
-rwx------  1 bob ldapusers  190 2005-02-14 00:42 me.txt
drwxr-xr-x  2 bob ldapusers 4.0K 2005-02-14 00:47 subdir
```

User 'bob' has 3 files and a subdirectory in the directory ~/permissions. Rights are in the first column. Lets split those strange signs.

```
D  U   G   O
- rwx r-- r--
```

The first sign tells you whether it is a directory (D) or not (-). The next signs give the permissions for 3 groups. From left to right:

* the owner permissions (user)
* the group permissions (group)
* permissions for all other users on the system (others)

There are 3 different rights possible: read (r), write (w) and execute (x). Theoretically this gives us 8 different combinations of permissions: rwx, rw-, r-x, -wx, r--, -w-, --x, ---.

In the file group.txt in the example above, the owner and all members of his group can read and write, but no one can execute the file. Other users cannot access it.

To change permissions on files use the `chmod` command. In general form, the command will look something like this: `chmod [ugoa][=+-][rwx] files`

First, you tell it who you want to assign rights: user, group, others or all (u,g, o or a). You may choose to overwrite existing rights (=), add (+) or take them away (-). Next, you provide the rights you want. As last argument, give one or more files to which you want to assign these rights.

To give yourself and members of your group read and write rights on OurClass.java just type:

```
$ chmod u=rw,g=rw,o= OurClass.java
$ ls -l OurClass.java
$ -rw-rw---- 1 bob ldapusers 0 2009-09-19 14:16 OurClass.java

```
For directories you can choose from the same set of permissions, but their meaning can be slightly different.

```
r:  read the contents of a directory (i.e. right to use ls)
w:  write (i.e. make or remove) files in the directory
x:  access to subdirectories (i.e. right to use cd)
```
Be advised that the only use of these letters is to make interpretation a little easier. In reality, the rights system is numeric: r equals 4, w equals 2 and x equals 1. The rights rwxrw---- can also be written as 760. When combining rights, just add their numeric values. Knowing this, we can also write the `chmod` command from above as follows: `chmod 660 OurClass.java`

## Standard input, output and error streams
In Linux, every program (process) gets 3 special files upon creation:

* standard input (stdin)
* standard output (stdout)
* standard error (stderr)

Most people call these files standard streams. These streams are used to transport data to and from processes. When a process is running, all three default streams are opened. The shell references these open files by standardized numbers called file descriptors (FD). The first one FD 0 refers to stdin, FD 1 refers to stdout and stderr is linked to FD 2.

* Standard input: this is the stream from which processes receive input, or the file in which a process can read input commands or data in general.
* Standard output: to this stream or open file, normal process output is sent.
* Standard error: processes are supposed to send errors and other diagnostic output to stderr.

In general, stdin comes from the keyboard and both stdout and stderr are sent to the screen or console terminal. If needed, it is possible to change this behavior. Consider this simple example: 

```
$ cat
hello
hello
stop repeating!
stop repeating!
<ctrl>+d
$
```

Why is the `cat` command acting like an annoying parrot? It reads a line from stdin, and echos it back to its stdout. If we do not specify a file when calling `cat`, input is coming from he keyboard, and each line of input is printed to stdout. `cat` stops doing this when it reads the end of file code, which can be entered using <ctrl>+d. By specifying an input file, `cat` no longer reads from stdin: `cat /etc/resolv.conf`

## Redirection to a file
We can save the output of a command to a file instead of showing it on the screen.

To save the contents of a directory in the file dirlist.txt, we redirect the output of ls to a file.

```
$ ls > dirlist.txt
```

Output normally sent to the screen is redirected by the > symbol and redirected to the file dirlist.txt. The given file is created, or overwritten if it already existed.

Appending output to a file can be achieved in this form:

```
$ ls >> dirlist.txt
```

Just like stdout, stdin can also be redirected. In the following example stdin will not be read from the keyboard but from a file.

```
$ wc -l < dirlist.txt
```

The command `wc` will process the contents of the file dirlist.txt as input. And the third default stream, stderr can be redirected using 2>. In general, redirecting a stream is accomplished using “FD>” where FD is the stream file descriptor (stdout=1,stderr=2). 

When this number is omitted the shell assumes redirecting stdout, thus file descriptor 1. To show that stdout and stderr are separate streams, we can give an inaccessible or nonexistent file as an argument to `cat`.

When stdout is redirected to the shadow.txt file, we still see the error message, because it is sent through stderr.

```
bob@system:~$ cat /abc
cat: /abc: No such file or directory
bob@system:~$ cat /etc/shadow > shadow.txt
cat: /etc/shadow: Permission denied
bob@system: ~$ ls -lh shadow.txt
-rw-r—r-- 1 bob ldapusers 0 2005-10-10 13:54 shadow.txt
```
It is also possible to redirect both stdout and stderr. The following example stores regular command output to the file ls_tmp.txt while errors are saved in ls_tmp_errors.txt.

```
ls -R /tmp > ls_tmp.txt 2> ls_tmp_errors.txt
```
Every Linux system has the special file /dev/null available. Data sent to this file just disappear. Some people refer to this file as 'the black hole'. At first sight it looks rather useless to make command output just disappear, but it can be very useful when using redirections. Redirecting the error stream to the black hole is done very frequently if these errors are not important, or if they are disturbing the readability of regular output. In the following example, all error messages from the command `ls` are sent to /dev/null.

```
ls -R /tmp > ls_tmp.txt 2> /dev/null
```
If you want to save both regular output and errors to the same file, you can redirect the error stream to the output stream with 2>&1 and then redirect stdout to a file.

```
ls -R /tmp 2>&1 > dirlist.txt
```

## Redirection to another command
One of the most used characters in the Linux shell is probably '|' which is referred to as the **pipe symbol**. With this command you can redirect the standard output of a command to the standard input of another command. This is an amazing and extremely powerful feature. Let's explain this with an example.

```
tree /usr
```

Obviously, the output of the command above is too big to fit on the screen. `less` is a command which can be used to read larger amounts of text. I would be nice if we could redirect the output of `tree` so it can be used as input for `less`. This is done by 'piping' the output of `tree` to `less` and makes the output easier to read:

```
tree /usr | less 
```

## Loops
To do some repetitive tasks one can use loops. There are different loop options available in Bash. 

A **for** loop: 

```
$ for i in $(ls /etc); do echo $i; done
```

Or with a **while** loop:
```
$ ls /etc | while read i; do echo $i; done
```
The while read approach is a bit more powerful. Consider the following example. First create some random files:

```
$ for i in $(seq 1 9); do touch "file $i”; done
$ ls -l
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 1 
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 2 
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 3 
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 4 
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 5 
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 6 
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 7 
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 8 
-rw-r--r-- 1 bob 9999    0 Feb 11 10:27 file 9
```
## Find
Searching for files and directories can be done using the find command.
In the next example, we look for all directories in /etc whose name start with 'cron':

```
$ find /etc -type d -name cron*
/etc/cron.daily
find: /etc/ppp/peers: Permission denied
/etc/cron.hourly
/etc/cron.weekly
/etc/cron.monthly
/etc/cron.d
find: /etc/chatscripts: Permission denied
```

Obviously, we cannot crawl through directories without the appropriate permissions. The message indicating we do not have sufficient rights is send to stderr. When we want to filter out these error messages we could redirect stderr as explained before as follows:

```
$ find /etc -type d -name cron* 2> /dev/null
/etc/cron.daily
/etc/cron.hourly
/etc/cron.weekly
/etc/cron.monthly /etc/cron.d
```

By combining find and xargs it is possible to execute a certain command for every file we find.

# Exercises
```
* Create the following files and directories in your homedir and give them correct permissions:
 -rwxrwxrwx  1 bob ldapusers    0 2005-02-14 02:48 file1
 -rw-r--r--  1 bob ldapusers    0 2005-02-14 02:48 file2
 -rw-------  1 bob ldapusers    0 2005-02-14 02:49 file3
 -r--r--r--  1 bob ldapusers    0 2005-02-14 02:49 file4
 drwxr-xr-x  2 bob ldapusers 4096 2005-02-14 02:49 subdir1
 drwxr--r--  2 bob ldapusers 4096 2005-02-14 02:49 subdir2
 dr--------  2 bob ldapusers 4096 2005-02-14 02:57 subdir3
* Create a subdirectory in your homedirectory with 'public' as name. Change its permissions so only you could write to it and all other users will have read access.
* Create a file in which everyone could write.
* List all home directories in /home which are open for other users (group or others should have r,w or x rights).
* List all lines in the file /etc/passwd containing the string ‘root’.
* Only show the columns with filenames and permissions of the files in your homedirectory. (tip: use ls, cut and/or tr)
* Sort the lines of previous question in reverse alphabetical order.
* Use the command cal to find out on which day of the week your birthday is in 2050 and write your output to a newly created file.
* Append the sentence "THE END" to the file you created in the previous exercise without opening this file in an editor.
* Create a subdirectory TEST in your homedir. Now create some files in it using this command line: for i in $(seq 1 9); do touch "file $RANDOM"; done && touch ‘filekeep me’ How can you remove all files except the file “filekeep me” with just one 'oneliner' or command?
* List only the name and permissions of the first 5 directories in /etc. Sample output:
  drwxr-xr-x /etc/ 
  drwxr-xr-x /etc/alternatives
  drwxr-xr-x /etc/apache2
  drwxr-xr-x /etc/apache2/conf.d
  drwxr-xr-x /etc/apache2/mods-available
* Same question as the question above, but now omit all error messages.
* List the name and permissions of all files in the /etc directory containing the word 'host' in their name.
* Use the command less to examine the contents of your history file. Note that recent commands are not yet saved. With the command history, the full history is displayed.
* Create a new empty file, you can choose a name, but prefix this name with the current date and time. Use this format: year.month.day.filename. Use the commands touch and date to accomplish this.
* What is the result of these 'oneliners'? Maybe you need to install fortune and cowsay. 
 $ cat /usr/share/dict/dutch | grep informatica > ~/test.txt
 $ cat /usr/share/dict/dutch | wc –l
 $ fortune -s | cowsay -f tux 
 $ date | cut -d " " -f 2,3 | tee today.txt
 $ head -n 50 /usr/share/dict/ducth | tr [a-z] [zyxwvutsrqponmlkjihgfedcba] > my_first_encription
 $ last | grep $USER
 $ last | grep $USER | wc -l 
 $ last | cut -d ' ' -f 1 | sort | uniq -c | sort -gr 
* Create the following directory and file structure:
  bob@system:/tmp/exercise $ tree.
  | -- dir1
  |    |--dir4
  |    |  ` -- file4
  |    ` -- file3
  | -- dir2
  | -- dir3
  | -- file1` -- file24
* Remove this complete structure using only one command.
* How much disk space uses the /var directory?
* Create an empty file 'me.txt' in your home directory with your name in it without using nano/vi/ed/... .
* Copy the file to 'we.txt' and add a few names (Pieter, Gerben, Tiebe, ...) to it using >> .
* Create 2 files with random text. Then create a third file containing the contents of the two other files.
* Give your VM an extra HDD of 4GB. Use lsblk to view the disk-path (/dev/sdb for instance). Format the disk with the command mkfs with an ext4 filesystem.
* Create a new folder /mnt/newlyaddHDD and mount the HDD in this folder with mount /dev/sdX /mnt/newlyaddHDD. Verify with df -h.
* Which exercise was difficult? Explain why.
```t
