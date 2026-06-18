# Introduction to the Command Line
Linux system administrators spend a significant amount of their time at a command line prompt. They often automate and troubleshoot tasks in this text environment. There is a saying, "graphical user interfaces make easy tasks easier, while command line interfaces make difficult tasks possible". Linux relies heavily on the abundance of command line tools. The command line interface provides the following advantages:

## No GUI overhead is incurred.
Virtually any and every task can be accomplished while sitting at the command line.
You can implement scripts for often-used (or easy-to-forget) tasks and series of procedures.
You can sign into remote machines anywhere on the Internet.
You can initiate graphical applications directly from the command line instead of hunting through menus.
While graphical tools may vary among Linux distributions, the command line interface does not.

## Terminal Emulator
A terminal emulator program emulates (simulates) a standalone terminal within a window on the desktop. By this, we mean it behaves essentially as if you were logging into the machine at a pure text terminal with no running graphical interface. Most terminal emulator programs support multiple terminal sessions by opening additional tabs or windows.
By default, on GNOME desktop environments, the gnome-terminal application is used to emulate a text-mode terminal in a window. Other available terminal programs include:
- xterm
- konsole (default on KDE)
- terminator

## Basic commands
There are some basic command line utilities that are used constantly, and it would be impossible to proceed further without using some of them in simple form before we discuss them in more detail. A short list has to include:

cat: used to type out a file (or combine files).
head: used to show the first few lines of a file.
tail: used to show the last few lines of a file.
man: used to view documentation.

## Command Line
Most input lines entered at the shell prompt have three basic elements:

Command
Options
Arguments
The command is the name of the program or script you are executing. 
