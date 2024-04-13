### 4- Archive and Compression
**1- Archiving Files**
An archive file is a collection of files and directories that are stored in one file. The archive file is not compressed — it uses the same amount of disk space as all the individual files and directories combined. A compressed file is a collection of files and directories that are stored in one file _and_ stored in a way that uses less disk space than all the individual files and directories combined. 

**Create an Archive File use Tar tool:**
tar is a short for **T**ape **Ar**chive, it is a computer software utility for collecting many files into one archive file, it was originally developed to write data to sequential I/O devices with no file system of their own, such as devices that use magnetic tape. 

```
tar -cf [archive name] [file(s)/location(s)]
```
where: 

 - **-c or --create** For creating new archive file
 - **-f** for archiving and creating files,
 `f` flag to _tell it that you will be working on files and not an ancient tape device_  (note that modern tape devices do exist for server back up purposes, but you will still need the `f` flag for them because they're now regular block devices in `/dev`
```
ziad@ziadpc:~/sysadmin_playground/comparch$ ls
total 16K
-rw-rw-r-- 1 ziad ziad    7 Apr 13 13:30 a
-rw-rw-r-- 1 ziad ziad   12 Apr 13 13:30 b
-rw-rw-r-- 1 ziad ziad   17 Apr 13 13:30 c
drwxrwxr-x 2 ziad ziad 4.0K Apr 13 13:31 dir
ziad@ziadpc:~/sysadmin_playground/comparch$ ls dir
total 8.0K
-rw-rw-r-- 1 ziad ziad 6 Apr 13 13:31 i
-rw-rw-r-- 1 ziad ziad 8 Apr 13 13:31 ii
ziad@ziadpc:~/sysadmin_playground/comparch$ tar -cf output.tar *
ziad@ziadpc:~/sysadmin_playground/comparch$ ls
total 28K
-rw-rw-r-- 1 ziad ziad    7 Apr 13 13:30 a
-rw-rw-r-- 1 ziad ziad   12 Apr 13 13:30 b
-rw-rw-r-- 1 ziad ziad   17 Apr 13 13:30 c
drwxrwxr-x 2 ziad ziad 4.0K Apr 13 13:31 dir
-rw-rw-r-- 1 ziad ziad  10K Apr 13 13:31 output.tar
ziad@ziadpc:~/sysadmin_playground/comparch$ file output.tar
output.tar: POSIX tar archive (GNU)
ziad@ziadpc:~/sysadmin_playground/comparch$ 
```
**Comparison between Sizes:**

```
ziad@ziadpc:~/sysadmin_playground/comparch$ ls
total 28K
-rw-rw-r-- 1 ziad ziad    7 Apr 13 13:30 a
-rw-rw-r-- 1 ziad ziad   12 Apr 13 13:30 b
-rw-rw-r-- 1 ziad ziad   17 Apr 13 13:30 c
drwxrwxr-x 2 ziad ziad 4.0K Apr 13 13:31 dir
-rw-rw-r-- 1 ziad ziad  10K Apr 13 13:31 output.tar
ziad@ziadpc:~/sysadmin_playground/comparch$ ls dir
total 8.0K
-rw-rw-r-- 1 ziad ziad 6 Apr 13 13:31 i
-rw-rw-r-- 1 ziad ziad 8 Apr 13 13:31 ii
ziad@ziadpc:~/sysadmin_playground/comparch$ 
```

you can see that tar maybe larger in size than the separate file, that is because:

 - `tar` archives have a minimum size of 10240 bytes by default, since The data in an archive is grouped into blocks, which are 512 bytes. Blocks are read and written in whole number multiples called _records_. 
With GNU `tar`, you can reduce this by specifying either a different block size, or different block factor, or both:
	```
	tar -cv -b 1 -f file.tar file.txt
	```

	The result will still be bigger than `file.txt`, because `file.tar` 	stores metadata about `file.txt` in addition to `file.txt` itself. In most cases you’ll see one block for the file’s metadata (name, size, timestamps, ownership, permissions), then the file content, then two blocks for the end-of-archive entry, so the smallest archive containing a non-zero-length file is four blocks in size (2,048 bytes with a 512-byte block). ([reference](https://unix.stackexchange.com/questions/685766/why-is-tar-archive-so-much-bigger-than-text-file-10240-bytes), [reference](https://www.gnu.org/software/tar/manual/html_node/Blocking-Factor.html))
	
 - in addition to storing metadata, tar for the tape convenience adds some zeroes at the end of every file and another set of zeroes at the end of the archive. because for tapes it is important to decide, where is the file end, and the device needs to know it even when searching (the tape moves faster). 
	 ```
	 ziad@ziadpc:~/sysadmin_playground/comparch$ cat file
	i
	a
	c
	ziad@ziadpc:~/sysadmin_playground/comparch$ tar -cf file.tar file
	ziad@ziadpc:~/sysadmin_playground/comparch$ hexdump file
	0000000 0a69 0a61 0a63                         
	0000006
	ziad@ziadpc:~/sysadmin_playground/comparch$ hexdump file.tar 
	0000000 6966 656c 0000 0000 0000 0000 0000 0000
	0000010 0000 0000 0000 0000 0000 0000 0000 0000
	*
	0000060 0000 0000 3030 3030 3636 0034 3030 3130
	0000070 3537 0030 3030 3130 3537 0030 3030 3030
	0000080 3030 3030 3030 0036 3431 3036 3436 3736
	0000090 3733 0037 3130 3430 3230 2000 0030 0000
	00000a0 0000 0000 0000 0000 0000 0000 0000 0000
	*
	0000100 7500 7473 7261 2020 7a00 6169 0064 0000
	0000110 0000 0000 0000 0000 0000 0000 0000 0000
	0000120 0000 0000 0000 0000 7a00 6169 0064 0000
	0000130 0000 0000 0000 0000 0000 0000 0000 0000
	*
	0000200 0a69 0a61 0a63 0000 0000 0000 0000 0000
	0000210 0000 0000 0000 0000 0000 0000 0000 0000
	*
	0002800
	ziad@ziadpc:~/sysadmin_playground/comparch$ 
	 ```
	you can see the block `0000200 0a69 0a61 0a63 0000 0000 0000 0000 0000
	0000210 0000 0000 0000 0000 0000 0000 0000 0000
	` has some zeros at then end.

**unarchive a file:**
	to unarchive a file to a specific directory:
	`tar -xf output.tar -C target_dir`

add the option `-x` which stands for extract, remove the option `-c` which is create option, and add option `-C` for specifying the target directory.
**Note**:  if `-C` is not passed the default target directory is `.`
	
	
	ziad@ziadpc:~/sysadmin_playground/comparch$ mkdir target_dir
	ziad@ziadpc:~/sysadmin_playground/comparch$ tar -xf output.tar -C target_dir
	ziad@ziadpc:~/sysadmin_playground/comparch$ ls target_dir/
	total 16K
	-rw-rw-r-- 1 ziad ziad    7 Apr 13 13:30 a
	-rw-rw-r-- 1 ziad ziad   12 Apr 13 13:30 b
	-rw-rw-r-- 1 ziad ziad   17 Apr 13 13:30 c
	drwxrwxr-x 2 ziad ziad 4.0K Apr 13 13:31 dir
	ziad@ziadpc:~/sysadmin_playground/comparch$ 


**2- Compress a file**
tar can compress a file by specifying a suitable compression algorithm 
| Short Option | Long Option | Algorithm |
|---|---|---|
| Z | --gzip | gzip |
| J | --bzip2 | bzip2 |
| j | --xz | xz |
| z | --compress | LZA (compress) |
|  | --lzip | lzip |
|  | --lzma | lzma |
|  | --zstd | zstd |

algorithms differ between each other in speed of compression and decompression, compression ratio, and being supported by other software (e.g. Linux kernel supports limited compression algorithms ).

some websites do a comparison between compression algorithms such as [this](https://www.privex.io/articles/which-compression-algorithm-tool/) and [this](https://forum.puppylinux.com/viewtopic.php?t=8375).
here is a paragraph from a [website](https://www.privex.io/articles/which-compression-algorithm-tool/).
> Summary - which compression algorithms are best for each usecase?
> Fastest compression and decompression: LZ4
> Highest overall compression ratio: XZ
> Compromise between compression ratio and speed: BZIP2 using LBZIP2
> Most widely used / compatible compression algorithm: GZIP

compression and decompression is done in the same manner as archiving.
```
	ziad@ziadpc:~/sysadmin_playground/comparch$ ls
	total 16K
	-rw-rw-r-- 1 ziad ziad    7 Apr 13 13:30 a
	-rw-rw-r-- 1 ziad ziad   12 Apr 13 13:30 b
	-rw-rw-r-- 1 ziad ziad   17 Apr 13 13:30 c
	drwxrwxr-x 2 ziad ziad 4.0K Apr 13 13:31 dir
	ziad@ziadpc:~/sysadmin_playground/comparch$ tar -cJf output.bzip2 *
	ziad@ziadpc:~/sysadmin_playground/comparch$ ls
	total 20K
	-rw-rw-r-- 1 ziad ziad    7 Apr 13 13:30 a
	-rw-rw-r-- 1 ziad ziad   12 Apr 13 13:30 b
	-rw-rw-r-- 1 ziad ziad   17 Apr 13 13:30 c
	drwxrwxr-x 2 ziad ziad 4.0K Apr 13 13:31 dir
	-rw-rw-r-- 1 ziad ziad  284 Apr 13 14:12 output.bzip2
	ziad@ziadpc:~/sysadmin_playground/comparch$ mkdir target_dir
	ziad@ziadpc:~/sysadmin_playground/comparch$ #decompress bzip2 file as xz file
	ziad@ziadpc:~/sysadmin_playground/comparch$ tar -xjf output.bzip2 -C target_dir/
	bzip2: (stdin) is not a bzip2 file.
	tar: Child returned status 2
	tar: Error is not recoverable: exiting now
	ziad@ziadpc:~/sysadmin_playground/comparch$ tar -xJf output.bzip2 -C target_dir/
	ziad@ziadpc:~/sysadmin_playground/comparch$ ls target_dir/
	total 16K
	-rw-rw-r-- 1 ziad ziad    7 Apr 13 13:30 a
	-rw-rw-r-- 1 ziad ziad   12 Apr 13 13:30 b
	-rw-rw-r-- 1 ziad ziad   17 Apr 13 13:30 c
	drwxrwxr-x 2 ziad ziad 4.0K Apr 13 13:31 dir
	ziad@ziadpc:~/sysadmin_playground/comparch$ 
	
```

