# Introduction to Command Line – Part 1

Created April 8, 2019      
Updated April 20, 2020

Created by Elizabeth Miller (millere@umn.edu)  

## Table of Contents

- [Introduction](#Introduction)
  * [What is the command line?](#What-is-the-command-line?)
- [The basics](#The-basics)
  * [Running the command line](#Running-the-command-line)
  * [The command line prompt](#The-command-line-prompt)
  * [Your first command](#Your-first-command)
  * [Helpful notes I](#Helpful-notes-I)
- [Basic navigation](#Basic-navigation)
  * [Files and folders](#Files-and-folders)
  * [Absolute paths](#Absolute-paths)
  * [Relative paths](#Relative-paths)
  * [Current working directory](#Current-working-directory)
  * [List files and directories](#List-files-and-directories)
  * [Change current working directory](#Change-current-working-directory)
  * [Helpful notes II](#Helpful-notes-II)
  * [Special file names](#Special-file-names)
  * [Command options](#Command-options)
  * [Using wildcards](#Using-wildcards)
- [Creating and editing files and folders](#Creating-and-editing-files-and-folders)
  * [Creating a directory](#Creating-a-directory)
  * [Creating a file](#Creating-a-file)
  * [Editing a file](#Editing-a-file)
  * [Viewing a file](#Viewing-a-file)
  * [Renaming or moving a file](#Renaming-or-moving-a-file)
  * [Copying a file](#Copying-a-file)
  * [Cleaning up](#Cleaning-up)
- [Permissions](#Permissions)
- [Getting help](#Getting-help)

<!-- toc -->

## Introduction

This tutorial is primarily a compilation of the following online resources:

- [Django Girls Tutorial, *Introduction to the command-line interface*](https://tutorial.djangogirls.org/en/intro_to_command_line/)
- [Donald Tournier, *Using the command line*](https://jdtournier.github.io/cmdline-tutorial/index.html)
- [Viking Code School Prep, *A Command Line Crash Course*](https://www.vikingcodeschool.com/web-development-basics/a-command-line-crash-course)
- [Minnesota Supercomputing Institute, *Introduction to Linux*](https://www.msi.umn.edu/tutorials/introduction-linux)

A huge portion of becoming proficient in command line programming is becoming familiar with commonly used commands. In this tutorial we will cover some of the basics:

- Basic navigation in command line
- Creating and editing files and folders
- File and folder permissions
- Getting help

### What is the command line?

The *command line* or command-line interface is a text-based application for viewing, handling, and manipulating files on your computer. More specifically, the command line lets you interact with the command language interpreter (or *shell*), which is a program that gives your commands to the operating system to perform. Most Unix-like operating systems, such as Mac OSX and Linux, use a shell program called Bash (*B*ourne *A*gain *SH*ell). 

## The basics

### Running the command line

On Mac OSX computers, the pre-installed `Terminal` application is the command-line interface and it uses the Bash shell. You should be able to find Terminal under Applications → Utilities → Terminal. The icon looks like this: 

<img src="https://help.apple.com/assets/5D92A6940946227D4301035B/5D92A6A50946227D43010362/en_US/2a48ebb241171912f1b2f30fc08a69f0.png" width="100">

On Windows computers, there is a pre-installed command-line interface, but unfortunately this does not use Linux. To run Linux commands on a Windows computer, you could try one of the options outlined [here](https://itsfoss.com/run-linux-commands-in-windows/). To access a *remote* Linux/Unix-based server from Windows using the command line (not covered in this tutorial), you need to download and install a command line application such as [PuTTy](https://www.chiark.greenend.org.uk/~sgtatham/putty/).

### The command line prompt

Once you open your command line, you should see a white or black window pop up with some text at the top:

<img src=https://github.com/JohnsonSingerLab/Tutorials/blob/master/Intro_to_Command_Line/images/command_line_window.png  width="600">

This is where you will type your commands. 

Each command will be prepended by a `$`. The part up to and including the `$` is called the *command line prompt*, or prompt for short. It prompts you to input something there. Notice that before the `$` there is a string of text that should include your computer user name. My Mac user name is *elizabethmiller* so my prompt says: `stp-er-10-129-100-215:~ elizabethmiller$`. For the remainder of this tutorial, we will use `YOURNAME` to represent your computer user name. For running the code on your computer, you will need to change `YOURNAME` to your actual user name.

In this tutorial, we will include the `$` when we show you a command. When you type out the command in your own command line window, ignore the `$` and only type the command.

### Your first command

Let’s start by typing this command:

```
$ whoami
```

Then press the `return` key. You should see your computer user name printed to the screen:

```
YOURNAME
```

You just completed your first command!

### Helpful notes I

- Try to type out each command; do not copy-paste. You'll remember more this way.
- You can keep a log of commands you run in a text-edit program such as Mac’s TextEdit. However, make sure the file is in *Plain Text* format and has automatic spelling correction turned off. You can make these changes by going to TextEdit → Preferences…
- When you run commands, there is rarely any feedback that you were successful. If the prompt just shows up again on the next line, that's a good sign – it was successful. This takes some getting used to!
- You can’t use a mouse or trackpad in command line, but you can navigate using the arrow keys.
- If you type in a command and then decide to terminate the process, you can hit `control` + `C` keys. For example, type the command `cat` and hit `return`. The `cat` command requires at least one argument (more on `cat` later!), but since we didn’t specify any here, `cat` is just waiting for an input from the keyboard. We can end this waiting game with `control` + `C`. Once you hit those two keys, you should see our `$` prompt reappear on the next line.

## Basic navigation

### Files and folders

Files and folders are stored on computers using a folder or *directory* structure. For example, on a Mac computer, you will have a pre-installed folder called `Documents`, within which you might have a number of folders, and within them some more folders, etc. 

The easiest way to visualize the directory structure is to think of it as a tree. If you listed the contents of the root folder (the root of the tree), you would find a number of other folders (the main branches). These folders might contain more folders (smaller branches) and/or files (leaves). This is illustrated below:

<img src=https://github.com/JohnsonSingerLab/Tutorials/blob/master/Intro_to_Command_Line/images/directory_structure.png  width="300">

Specifying a file or folder is simply a matter of providing enough information to uniquely identify it. Here, the folder called `Data` can be uniquely identified by starting from the `Root Directory`, going into `Users`, then `YOURNAME`, then `Desktop`, to find `Data`. This process can be thought of as specifying the *path* to the file or folder of interest. In fact, this is the exact term used in Unix jargon, essentially meaning 'an unambiguous file name'. Thus, specifying a file name boils down to providing a unique, unambiguous path to the file. Think of it like a map you’re providing to the computer to tell it where a specific file or directory is.

### Absolute paths

On Unix, the root of the tree is always referred to using a simple forward slash (`/`). Folders are referred to using their names, and are delimited using `/`. For example, the full, absolute path to the folder `Data` in the figure above is:

```
/Users/YOURNAME/Desktop/Data
```

This simply means: starting from the root folder (`/`), go into folder `Users`, then `YOURNAME`, then `Desktop`, to find `Data`. This is an example of an ***absolute path***, because the start point of the path (the root folder) has been specified within the file name. Thus, an absolute path must start with a forward slash - if it does not, it becomes a *relative path*, explained below.

### Relative paths

***Relative paths*** are so called because their starting point is the current folder (called the *current working directory*) you are in, rather than the root folder. Thus, they are relative to the current working directory, and only make sense if the working directory is also known.

For example, let’s say our current working directory `Desktop`. In this folder there may be a number of other files and folders, as illustrated in the figure above. Since the folder `Data` is in the current working directory, it can be referred to unambiguously using the relative path `Data`, rather than its full absolute path `/Users/YOURNAME/Desktop/Data`. As you can see, this requires considerably less typing.

When you specify a relative path, it will actually be converted to an absolute path, simply by taking the current working directory (an absolute path), appending `/`, and appending the relative path you supplied after that. For example, if you supply the relative path `Data`, the system will add up `/Users/YOURNAME/Desktop` + `/` + `Data` to give the absolute path `/Users/YOURNAME/Desktop/Data`.

Since the system simply adds the relative path to the working directory, you can see that files and folders further along the directory tree can also be accessed easily. For example, if your current working directory is `YOURNAME`, you can specify the `Papers` folder within the `Desktop` folder using the relative path `Desktop/Papers`.

Of course, if you changed your current working directory, the relative path would need to change accordingly. Using the same example as previously, if `Desktop` was now your current working directory, you could use the simpler relative path `Papers` to refer to the same folder.

### Current working directory

Now that we have an understanding of the directory structure, let’s see where we are. In your command line window, type `pwd` after the prompt and hit `return`:

```
$ pwd
/Users/YOURNAME
```

`pwd` stands for 'print working directory'. You can see that my current working directory is `YOURNAME` within the `Users` directory. This is your home directory and where you will start every time you open a new command line window.

### List files and directories

Now that we know what our current working directory is, let’s see what files and folders are in it. Type `ls`, which stands for ‘list segments’, after the prompt and hit `return`:

```
$ ls
Desktop
Downloads
Music
```

Based on the example directory structure illustrated above, there are three directories within the current working directory: `Desktop`, `Downloads`, and `Music`. 

### Change current working directory

Now that we know what folders are in our current working directory, let’s change our current working directory to one of these directories. To do this, you type `cd` (‘change directory’), a `space`, and then the name of the directory you want to change to. Let’s change our working directory to `Desktop`:

```
$ cd Desktop
```

Now let’s use the command `pwd` again to check if we’ve really moved:

```
$ pwd
/Users/YOURNAME/Desktop
```

Yep! We have successfully changed our working directory from `YOURNAME` to `Desktop`.

Now let’s use the `ls` command to see what’s in the `Desktop` directory:

```
$ ls
Analyses
Data
dunn_et_al_2019.pdf
duplicates.xlsx
manuscript1.docx
manuscript2.docx
Meetings
Papers
```

There appears to be four directories in the `Desktop` directory: `Analyses`, `Data`, `Meetings`, and `Papers`. There are also several files: `dunn_et_al_2019.docx`, `duplicates.xlsx`, `manuscript1.docx`, and `manuscript2.docx`. Remember that what you have in your `Desktop` directory won't be the same as this example!

### Helpful notes II

- Capitalization matters!  `Desktop` and `desktop` are not the same thing.
- Spaces matter! You'll quickly learn to omit spaces in your file and folder names because they're a pain to type on the command line -- you have to "escape" them with an extra backslash `\`, so `My Folder Name` becomes `My\ Folder\ \Name`. Otherwise the computer will think you're passing multiple arguments or commands.
- When you're typing file names (and many other things) on the command line, use the `tab` key to autocomplete the word. This will save you tons of time. It will automatically fill in the remainder of the word once you've typed enough to uniquely identify it.
- Pressing the ▲ (`up`) key will cycle through the history of recently used commands. Very handy.
- Typing `clear` clears out all the recent output, though you can still scroll up to see it.
- `#` is a special character in shell scripting. It is used to ‘comment out’ a line. Whatever is written after a `#` on a line is not evaluated by the shell. For example, if you run the following line, notice that the `ls` command is not executed:

```
$ # ls
```

Adding comments to your scripts is a great way to keep track of what you’re doing. Otherwise you may come back to the script months later and not remember what you did (extremely frustrating!). For the remainder of this tutorial, when there are multiple lines of code, we will include `#` commented lines to help explain the steps.

### Special file names

There are a few file name shortcuts that will be particularly helpful when navigating through directories. These are:

#### 1. `~` (tilda)

`~` is shorthand for your home folder. This is your `YOURNAME` directory. If you wanted to change your working directory back to my home folder, you could type:

```
$ cd ~
```

This will take you back to `YOURNAME`. Try this yourself and then use `pwd` to make sure you’re really in your home directory.

Additionally, you can use `~` to shorten absolute paths. If your present working directory is `YOURNAME` and you want to change it to `Papers`, I could use the `~` as a placeholder for the `/Users/YOURNAME` part of the path:

```
$ cd ~/Desktop/Papers
```

#### 2. `.` (single full stop) 

`.` represents the current working directory. For example, if your current working directory is `Desktop`, you can refer to the `Papers` folder by specifying `./Papers`.

#### 3. `..` (double full stop) 

`..` represents the *parent* folder of the current directory. For example, if my current working directory is `Papers` (absolute path `/Users/YOURNAME/Desktop/Papers`), I can refer to the `Data` folder (absolute path `/Users/YOURNAME/Desktop/Data`) using the relative path `../Data`. This shortcut essentially means "drop the previous folder name from the path", or "go back down to the previous branch". 

`..` is also useful for moving between directories with `cd`:

```diff
# check working directory
$ pwd
/Users/YOURNAME/Desktop/Papers
# change working directory to parent
$ cd ..
# check working directory again
$ pwd
/Users/YOURNAME/Desktop
```

### Command options

Many commands will have options that you can specify, which are preceded by a single dash (`-`) or double dash (`--`). For example, adding the option `-a` after the `ls` command will display not just the normal files and folders in your current directory, but also the hidden ones. Hidden file names are preceded by a `.` and are usually things that users shouldn't mess with. Let’s try `ls -a`:

```
$ ls -a
.Rhistory
.localized
Analyses
Data
dunn_et_al_2019.pdf
duplicates.xlsx
manuscript1.docx
manuscript2.docx
Meetings
Papers
```

You can see that using the `-a` option showed us two additional hidden folders: `.Rhistory` and `.localized`. Again, remember that what you have in your directory won't be the same as this example.

Keep in mind that since options are, well, optional, you don't always see them. Also, remember that options are specific to each command and you cannot use an option from one command with another command. For example, the `-a` option from `ls` might mean something very different or not even exist in another command. 

### Using wildcards

There are a number of characters that have special meaning to the shell. Some of these characters are referred to as *wildcards*, and their purpose is to ask the shell to find all file names that match the wildcard, and expand them on the command line. This use of wildcards becomes very useful when dealing with folders containing large numbers of similar files, and only a subgroup of them is of interest. Although there are a number of wildcards, the only one that will be detailed here is the `*` character.

The `*` character essentially means 'any number or any characters'. When the shell encounters this character in an argument, it will look for any files that match that pattern, and append them one after the other where the original pattern used to be. This can be better understood using some examples.

Within the current working directory, `Desktop`, we have the files `dunn_et_al_2019.pdf`, `duplicates.xlsx`, `manuscript1.docx`, and `manuscript2.docx`, as well as several folders. If we simply list the files (using the `ls` command), we would see:

```
$ ls
Analyses
Data
dunn_et_al_2019.pdf
duplicates.xlsx
manuscript1.docx
manuscript2.docx
Meetings
Papers
```

But let's say we only wanted to list the Excel files. Then we could use a wildcard, and specify that we are only interested in files that end with `.xlsx`:

```
$ ls *.xlsx
duplicates.xlsx
```

As another example, maybe we're only interested in Word files that start with the word ‘manuscript’. In this case, we could type:

```
$ ls manuscript*.docx
manuscript1.docx
manuscript2.docx
```

## Creating and editing files and folders

As you get more advanced in your scripting, there will be occasions when you will want to make and alter directories and files. Creating directories is an especially important skill to learn as directories will help you organize your files.

### Creating a directory

How about creating a practice directory on your desktop? First, change your working directory to your desktop (remember to add a path if necessary).

```
$ cd ~/Desktop
```

Then, type the command `mkdir` (‘make directory’), `space`, and the name of the folder you want to create. Remember to avoid spaces in your directory (and file) names. For this example, let's name our new directory "practice".

```
$ mkdir practice
```

The `mkdir` command has now created a folder with the name `practice` on your desktop. You can check if it's there by manually looking on your desktop and/or by running the `ls` command. This directory is just like any other directory on your computer.

Let’s make this new directory our current working directory:

```diff
# change directory
$ cd practice
# check working directory
$ pwd
/Users/YOURNAME/Desktop/practice
```

Let’s see what’s in there:

```
$ ls

```

Since we just created the folder, it should be empty.

### Creating a file

Now let’s create a new file called `test.txt` in our new directory using the `touch` command. `touch` gently “touches” the file – if the file already exists, it updates the “last modified” date to the current date and time. If the file doesn’t exist, touch will create the blank file:

```
$ touch test.txt
```

Now if we look in the `practice` directory we should see the file we just created:

```
$ ls
test.txt
```

There is it!

### Editing a file

We’ve created a file, but like the directory we created, our new file is currently empty. Let’s add some text to it. Since we’re working locally on our computers, you could simply open the file by double clicking the icon in the `practice` directory on your desktop.

However, when we’re eventually working on remote servers, this won’t be an option. Therefore, let’s try editing the file in command line using the command `nano`. `nano` is a simple text-editor that you can run in command line. To start editing our `test.txt` file with `nano`, we simply type:

```
$ nano test.txt
```

You should now be on the default `nano` screen:

<img src=https://github.com/JohnsonSingerLab/Tutorials/blob/master/Intro_to_Command_Line/images/nano_main.png width="600">

At the top, you’ll see the name of the program and version number, the name of the file you’re editing, and whether the file has been modified since it was last saved. Next, you’ll see the contents of your document, a body of text. In our case, this is blank since our file is empty. The two rows at the bottom are what make this program very user-friendly: the shortcut lines.

Let’s add some text to the file so we have something to play with later. Just type or copy and paste some text into the terminal window:

<img src=https://github.com/JohnsonSingerLab/Tutorials/blob/master/Intro_to_Command_Line/images/nano_text.png width="600">

Now let’s use one of `nano`’s handy shortcuts to save the file and exit out of the text-editor. Type `control`+`X`. Note that `nano` does not use the `shift` key in shortcuts. All shortcuts use lowercase letters and unmodified number keys, so `control`+`X` is NOT `control`+`shift`+`X`.

<img src=https://github.com/JohnsonSingerLab/Tutorials/blob/master/Intro_to_Command_Line/images/nano_save.png width="600">

After typing `control`+`X`, `nano` asks us whether we want to save the changes we’ve made to our file before exiting. We do want to save our edits, so we type `y`:

<img src=https://github.com/JohnsonSingerLab/Tutorials/blob/master/Intro_to_Command_Line/images/nano_filename.png width="600">

`nano` is now giving us the option to change the name of the file. In this case, we don’t want to so we simply hit `return`. Voila! The `nano` screen closes and we’re back to our standard terminal window. Just to check, let’s make sure the file we just edited is still there:

```
$ ls
test.txt
```

Yep! It’s still there.

### Viewing a file

Now that we’ve added some text to `test.txt` using `nano`, let’s view it! There are a number of commands that we could use.

#### `cat`

If we want to read the file by dumping its contents directly into the terminal, use the `cat` (‘concatenate’) command:

```
$ cat test.txt
This
is
a
test
file.
```

#### `head` and `tail`

`cat` works well for small files like the one we just created, but for larger files (such as FASTQ or FASTA files) this command will dump *a lot* of text onto your screen and will not be very useful.

A quick way to view just a part of a file is with the `head` and `tail` commands. `head` will print the first *X* lines of a file. By default, the first 10 lines are printed, but more or less can be specified with the `-n` option. 

```
$ head test.txt
This
is
a
test
file.
```

Since we only have five lines, all five are printed by default. Let’s try only printing only the first two lines:

```
$ head -n 2 test.txt
This
is
```

Similar to `head`, the `tail` command prints the last *X* lines of a file. Again, by default, only the last 10 lines are printed, but more or less can be specified with the `-n` option.

```
$ tail -n 3 test.txt
a
test
file.
```

#### `less` and `more`

If you want to view the entire file and be able to scroll through it, you can use the `less` or `more` commands:

```
$ less test.txt
```

A new screen should appear that shows your entire file. For files longer than the length of the window, you can navigate through the file line by line pressing `return` key or the ▲▼ (`up`/`down`) keys. To navigate one page at a time, use the `spacebar`. To exit the `less` command, type `q` (‘quit’).

The `more` command is similar to `less`, but loads the entire file contents at once onto your screen. Again, type `return` to navigate line by line, use the `spacebar` to navigate one page at a time, and type `q` to exit.

### Renaming or moving a file

The ‘move’ command, `mv`, will let you either rename a file (by ‘moving’ it to the same location but with a different name) or actually move it (by specifying a different target directory).

Let’s rename `test.txt` as `test2.txt`. The `mv` command expects that the first file listed (or the first *argument*) will be the original file name and the second file will the new name:

```
$ mv test.txt test2.txt
```

Check to see that the file name has changed:

```
$ ls
test2.txt
```

Now let’s create a new directory within the `practice` directory and then use the `mv` command to actually move the renamed file into this new directory. The first argument listed should be the file to be moved and the second argument is where that file should be moved.

```diff
# make directory new_folder
$ mkdir new_folder
# list what's in new_folder
$ ls
new_folder
test2.txt
# move test2.txt to new_folder
$ mv test2.txt new_folder
# list what's in new_folder again
$ ls
new_folder
```

The `new_folder` has been created and the `test2.txt` file has been moved into `new_folder`. We can see that it’s in `new_folder` by typing:

```
$ ls new_folder
test2.txt
```

Alternatively, we can change our current working directory to `new_folder` and then use `ls`.

```diff
# change working directory to new_folder
$ cd new_folder
# list what's in new_folder 
$ ls
test2.txt
```

### Copying a file

Use `cp` to ‘copy’ a file to a new location or name, with the same syntax as `mv`. Let’s create a copy of `test2.txt` in the original `practice` directory:

```diff
# copy test2.txt to the practice directory
$ cp test2.txt ~/Desktop/practice
# list what's in the practice directory
$ ls ~/Desktop/practice
new_folder
test2.txt
```

Note that both `cp` and `mv` can be destructive because if you try to move or copy a file onto one that already exists, it will do so without warning you. Use the `-i` option to make sure it warns you if you're about to overwrite something. For example, let’s try to create a new copy of `test2.txt` within the `practice` directory (you should already have `test2.txt` in `practice`):

```diff
# check current working directory
$ pwd
/Users/YOURNAME/Desktop/practice/new_folder
# list what's in current directory
$ ls
test2.txt
# try to copy file test2.txt to practice (with -i option)
$ cp -i test2.txt ..
overwrite ../test2.txt? (y/n [n])
# try to copy file test2.txt to practice (without -i option)
$ cp test2.txt ..
```

As you can see, including the `-i` option prompts you with whether you want to overwrite the `test2.txt` in the `practice` directory. When we *don't* include the `-i` option, the version of `test2.txt` in `practice` is overwritten by the version in `new_folder`. Luckily, these files' contents are identical so no harm is done, but you could see how this could be a problem if you had two different files with the same name.

### Cleaning up

Speaking of destructive commands, if you want to intentionally remove a file, pass its path and/or file name to the `rm` (‘remove’) command:

```diff
# list what's in current directory
$ ls
test2.txt
# remove the file test2.txt
$ rm test2.txt
# list what's in current directory again
$ ls

```

The file is gone!

**Attention: Deleting files using `rm` is irrecoverable, meaning the deleted files will be gone forever! So be *very* careful with this command.**

You won't be able to remove a directory that contains files unless you include the `-r` option. Only do this if you're absolutely sure! This is one of those commands that can delete a lot of files very quickly from your hard drive.

Let’s delete the `new_folder` directory we created with `mkdir` earlier. First we need to change our current working directory to something other than the folder being deleted. Then we can delete the directory:

```diff
# check present working directory
$ pwd
/Users/YOURNAME/Desktop/practice/new_folder
# change directory to the parent directory
$ cd ..
# check present working directory again
$ pwd
/Users/YOURNAME/Desktop/practice
# list what's in working directory
$ ls
new_folder
test2.txt
# remove new_folder
$ rm -r new_folder
# list what's in working directory again
$ ls
test2.txt
```

## Permissions

The last topic we’re going to briefly touch on is permissions. In Linux, every file and directory belongs to a user and a group. Files and directories have a set of permissions controlling who can read (`r`), write (`w`), or execute (`x`) them.

- The ***read*** permission means that a user has the right to open a file or directory and view the contents.
- The ***write*** premission means that a user has the right to make changes to a file or directory. Normally, you will not want others to be allowed to make changes to your files, so typically by default write permission is only allowed to the owner.
- The ***execute*** permission means that a user has the right to run a file that works like a command. 

To see the files in the current working directory along with their permissions, use the `ls` command with the `-l` option. For example, let’s see the permissions of the files and directories on the `Desktop` from our example directory structure.

```diff
# change directory to desktop
$ cd ~/Desktop
# list permissions for all files and folders in Desktop
$ ls -l
 drwxr-xr-x  20 YOURNAME  student      680 Mar 11 11:31 Analyses
 -rw-r--r--@  1 YOURNAME  student  4514424 Apr 19 10:28 dunn_et_al_2019.pdf
 -rw-r--r--@  1 YOURNAME  student     9132 Apr 17 13:59 duplicates.xlsx
 drwxr-xr-x  22 YOURNAME  student      748 Apr 19 10:35 Data
 -rw-r--r--@  1 YOURNAME  student    13468 Apr  9 15:25 manuscript1.docx
 -rw-r--r--@  1 YOURNAME  student   216012 Apr  1 13:06 manuscript2.docx
 drwxr-xr-x  26 YOURNAME  student      884 Feb  1 16:15 Meetings
 drwx------  10 YOURNAME  student      340 Sep 29  2018 Papers
```

This can be rather difficult to read because some of the lines may be too long to fit onto one line of the terminal window. To fit more text on each line, you can manually make the window bigger and then run the command again.

There should be one line for each file and folder within the `Desktop` directory. The permissions information for each file or folder is in the first 10 characters of each line. The first position indicates whether the object is directory (`d`) or file (`-`). The next positions are in groups of three corresponding to *u*ser, *g*roup, and *a*ll (everyone else).

In the above example, the `Analyses` directory has the permissions string `drwxr-xr-x`. This tells us `Analyses` is a directory; the owner is `YOURNAME`; the owner has read, write, and execute permission; the group has read and execute permission; all others just have execute permission. 

To change the permission of a file or directory, use the command `chmod` (‘change mode’). Use a `+` or `-` (plus or minus sign) to add or remove permissions for a file, respectively. Use an equals sign `=` to specify new permissions and remove the old ones for the particular type of user(s). You can use `chmod` letters where the letters are: `a` (all [everyone]), `g` (group), and `u` (user).

**Some examples:**

Give your group read and write permissions for the `Papers` directory:

```
$ chmod g+rw Papers
```

Remove read permissions from `duplicates.xlsx` for all users within your group (not including you):

```
$ chmod g-r duplicates.xlsx
```

Give everyone write and execute permission for `dunn_et_al_2019.pdf`:

```
$ chmod a+wx dunn_et_al_2019.pdf
```

Give everyone write permission to `Data`, if anyone had execute permission it would now be removed:

```
$ chmod a=rw Data
```

Give all permissions to `Papers` plus all permissions to all files and folders within `Papers`:

```
chmod -R 777 Papers
```

The `-R` option stands for 'recursive'. In Linux/Unix, a *recursive* command works on a specified directory, and if a directory has subdirectories and files, the command works on those files too. 

## Getting help

If this all feels a bit overwhelming, that’s ok! You are essentially learning an entirely new language and it will take time for you to feel comfortable with it. As with learning anything new, practice is essential.

If you are not sure how to do a specific task, Google can be your best friend. The same goes for confusing error messages (of which there are many). Frequently you can find an answer simply by copying and pasting the error message into the Google search bar.

There are also many great online tutorials that cover much more than what we’ve gone over here. In addition to the tutorials listed at the top of page 1, www.codecademy.com is a great resource. Obtain a free account at this site, navigate to "Catalog" in the upper right corner of the home page, then click on "Learn the command line" module.








