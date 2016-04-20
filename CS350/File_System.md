# File System

## File Interface

* open, close
* read, write, seek
* get/set file meta-data

## File Position

* each file descriptor has an associated file position
* read and write operations start from the current file position and update the current file position
* this makes sequential file I/O easy for an application to request
* for non-sequential file I/O: seek operation to adjust file position and positioned read or write operation

## Directories and File Names

* a directory maps file names (strings) to i-numbers
* an i-number is a unique (within a file system) identifier for a file or directory
* giben an i-number, the file system can find the data and meta-data for the files

Since directories can be nested, a filesystem can be viewed as a tree, files are leaves.
Applications refer to files using pathnames, not i-numbers

## Hard Links

* a hard link as an association between a name (string) and an i-number.
* each file has a unique i-number, but may have multiple pathnames (hard links)
* hard links can be removed
* while link counter > 0, file is still there; delete if file counter == 0
* hard links must refer to existing files
* i-number of the hard link is the i-number of the referred file

## Symbolic Links (soft link)

* a symbolic link is an association between a name and a pathname
* if a application attempt to open a symbolic link, the file system will
	1. recognize the symbolic link
	2. attempt to open the referenced file
* symbolic link may not refer to existing files
* i-number of the symbolic link is a new i-number

## Multiple File Systems

mounting does not make two file systems into one file system

* it merely creates a single, hierarchical namespace that combines the namespaces of two file systems
* the new namespace is  tempoaray

* hard link cannot cross file system boundaries
* soft link do not have this limitation

## File System

persistent information

* file data
* file meta-data
* directories and links
* file system meta-data

non-persistent information

* open files per process
* file position for each open file
* cached copies of persistent data

* most disks have a sector size of 512 bytes, disk is sector addressable (memory is byte addressable)
* group every 8 consecutive sectors into a block
	* better spatial locality
	* reduces the number of block pointers
	* 4 kb block is a convenient size for demand paging

## i-node

An i-node is a fixed size index structure that holds both file meta-data and a small number of pointers to data blocks

the maximum file size = i-node size / direct pointer size * block size

## Indirect Blocks

Indirect pointer points to a block full of direct pointers

4 kb block of pointers = 1024 pointers

### Double indirect points

points to a 4 kb block of indirect pointers

## Directories

Directory file contains directory entries, each consisting of a file name and the corresponding i-number

