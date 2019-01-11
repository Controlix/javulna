# Exercise 1
Use sql-injection to execute custom queries to access the database name, table names, etc.
I also used sqlmap tool to automate the sql-injection attack.

## Fix
use a prepared statement instead of a sql string

# Exercise 2
This is just a required action to perform the other exercises.

# Exercise 3
Add xml in the password query parameter so the replacement username/password is picked from this input.
E.g. newPassword=aPassword</pwd><userName>anotherUser</userName><pwd>

## Fix
allow only alfanumeric values for the new password

# Exercise 4
Use number overflow to get a negative price for an item.
Enter a large number for the amount of expensive items so that the price exceeds the maximum integer value i.e. 2**31-1.

## Fix
use a long to calculate the price

# Exercise 5
Use path traversal to download files outside the allowed folder.
By using a filename like ../../../something/file.ext you can read a file that is not in the applications' file space.

## Fix
do not allow a path in the download filename

## Other
- you can upload a file that is infected by a virus
  - place uploaded files in a temporary location that is scanned for virusses and only move safe files to the application file space
- you can upload a huge file filling the disk space
  - put a limit on the size of the file to upload
- you can upload an unrestricted amount of files so you use up all nodes on the file system
  - put a limit on the number of files in the applications' file space
- you can trick the user by providing a wrong file extension
