# The Find command cheat sheet

This is helpful `find` command for linux guys, and important for Digital Forensic Team.


## 1. Basic find command
```shell 
find -name "TestFile"
```

## 2. Find Files Using Name and Ignoring Case
```shell 
find -iname "TestFile"
```

## 3. Limit Search To Specific Directory Level Using mindepth and maxdepth

```shell 
find / -maxdepth 3 -name passwd
-maxdepth --> will go 3 directories below -- / 1st; /etc 2nd; /usr/bin 3rd
```

```shell 
find / -mindepth 3 -maxdepth 5 -name passwd
```
will go 3 depths first and upto 5 -- so will not disply under /; /usr; /usr/bin

## 4. Executing Commands on the Files Found by the Find Command.
```shell
user -exec {} /;
find -iname "TestFile" -exec md5sum {} \;
```

## 5. Inverting the match.
To inver the match use the "-not" switch
```shell
find / -not -iname "TestFile"
```

## 6. List inodes of the files
```shell
ls -i1 test*
16187429 test-file-name
16187430 test-file-name
```
```shell
find -inum 16187430 -exec mv {} new-test-file-name \;
ls -i1 *test*
16187430 new-test-file-name
16187429 test-file-name
```

## 7. Find file based on the File-Permissions
You can :
* Find files that match exact permission
* Check whether the given permission matches, irrespective of other permission bits
* Search by giving octal / symbolic representation

```shell
find . -perm -g=r -type f -exec ls -l {} \;
```

Will display all files with group permission read. Not files with readonly group permission

```shell
find . -perm g=r -type f -exec ls -l {} \;
```
Will dispay files with 040 permission. i.e files with group read only permisison

```shell
find . -perm 040 -type f -exec ls -l {} \;
```
Will dispay files with 040 permission. i.e files with group read only permisison


## 8. Find all empty files (zero byte file) in your home directory and its subdirectory
```shell
find ~ -empty
```

Need to check diff
```shell
find . -empty -not -name "test" -- name not equal to test
find . -not -empty -name ".*"   -- no empty files
```

## 9. Finding the Top 5 Big Files

```shell
find /var -type f -exec ls -s {} \; | sort -n -r | head -5
find /var -type f -size +10M -exec ls -lh {} \; 
```

## 10. Finding the Top 5 Small Files
```shell
find /var -type f -exec ls -s {} \; | sort -n | head -5
```
This will most probably list zero byte files or empty files!!! 

So to list the smaller files other than the ZERO byte files.
```shell
find /var -not -empty -type f -exec ls -s {} \; | sort -n | head -5
```

## 11. Find Files by Size
use the `-size` switch `-- +` for greater than; `-` for lesser than
Find files bigger than the given size
```shell
find / -size +100M
```

Find files smaller than the given size
```shell
find / -size -100M
```

Find files that matches the exact given size
```shell
find / -size 100M
```

Note: `–` means less than the give size, `+` means more than the given size, and no symbol means exact given size.

## 12. Find files based on file types
use `-type` switch

Find only the socket files.
```shell
find . -type s
```

Find all directories
```shell
find . -type d
```

Find only the normal files
```shell
  find . -type f
```

Find all the hidden files
```shell
find . -type f -name ".*"
```

Find all the hidden directories
```shell
find -type d -name ".*"
```

## 13. Creaing aliases:
Use the alias command
```shell
alias lslarge="find /var -type f -size +10M -exec ls -lh {} \;"
lslarge -- will display greater than 10MB files in /var
```

## 14. Find Files Based on Access / Modification / Change Time

You can find files based on following three file time attribute.
1. Modification time of the file. Modification time gets updated when the file content modified.
2. Access time of the file. Access time gets updated when the file accessed.
3. Change time of the file. Change time gets updated when the inode data changes.

```
# min argument treats its argument as minutes. For example, min 60 = 60 minutes (1 hour).
# time argument treats its argument as 24 hours. For example, time 2 = 2*24 hours (2 days)

* -mmin n File’s data was last modified n minutes ago.
* -mtime n File’s data was last modified n*24 hours ago.

* -amin n File was last accessed n minutes ago
* -atime n File was last accessed n*24 hours ago

* -cmin n File’s status was last changed n minutes ago. inode change
* -ctime n File’s status was last changed n*24 hours ago.
```

## 14. Find files whose content got updated/Modified within last 1 hour/1Day
```shell
find . -mmin -60
find / -mtime -1 # Finds all the files (under root file system /) that got updated within the last 24 hours (1 day)
```

## 15. Find files which got accessed before 1 hour/1Day
```shell
find . -amin -60 # Find files in the current directory and sub-directories, which got accessed within last 1 hour (60 minutes)
find / -atime -1
```

## 16. Find files which got changed exactly before 1 hour/1day :: inode change
```shell
find . -cmin -60
find / -ctime -1 # Finds all the files (under root file system /) that got changed within the last 24 hours (1 day).
```

## 17. Restricting the search only to unhidden files.
```shell
find . -mmin -15 \( ! -regex ".*/\..*" \)
```
Refer the regex statement

## 18. Find files modified/accessed/changed after modifying a refrence file
Syntax: `find -newer FILE`

```shell
find -newer /etc/passwd # Modified after
find -anewer /etc/hosts # Accessed after
find -cnewer /etc/fstab # Changed after
```

## 19. Searching Only in the Current Filesystem
use `-xdev` switch `--` Don’t descend directories on other filesystems.
```shell
find / -xdev -name "*.log"
```
Will search only `/`, not other mount points mounted under `/`.

```shell
find / -name "*.log"
```
This will search all FS starting from `/`.

## 20. Using more than one { } in same command
```shell
find -name "*.txt" cp {} {}.bkup \;
```

## 21. Redirecting errors to /dev/null
```shell
find -name "*.txt" 2>>/dev/null
```

## 22. Substitute space with underscore in the file name.
```shell
find . -type f -iname “*.mp3″ -exec mv “s/ /_/g” {} \;
```

