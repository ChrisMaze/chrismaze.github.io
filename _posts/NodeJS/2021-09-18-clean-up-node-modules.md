---
layout: single
title:  "Clean Up All Node_modules"
date:   2021-09-18 00:02:10 +0800
tags:
  - nodejs
  - linux
---
### Background
[最近分析师在Apple的iMessage中发现了重大漏洞](https://citizenlab.ca/2021/09/forcedentry-nso-group-imessage-zero-click-exploit-captured-in-the-wild/)   
于是打算升级系统到最新的Big Sur, 但是我的storage只剩下可怜的8GB了，升级系统需要预留35GB以上的空间。  
检查了storage占用后，发现node_modules大概占用了大于30GB的空间，并且分布在不同的folder下面。  
怎么快速清理所有的node_modules呢？   
手动删除是不可能手动的了，这辈子都不可能的， 写脚本吧。  

### Script
```shell script  

#!/bin/bash -e 

path=${1:unknow}

files=$(find $path -name "node_modules" -type d -prune)

for i in $files
do
  echo $i
  rm -rf $i
done
```

- Find Files by Type
Sometimes you might need to search for specific file types such as regular files, directories, or symlinks. In Linux, everything is a file.
To search for files based on their type, use the -type option and one of the following descriptors to specify the file type:
    > f: a regular file  
    d: directory
    l: symbolic link
    c: character devices    
    b: block devices  
    p: named pipe (FIFO)  
    s: socket  

- prune:  
The find command works like this:   
It starts finding files from the path provided in the command which in this case is the current directory(.).   
From here, it traverses through all the files in the entire tree and prints those files matching the criteria specified.   
-prune will not allow the find command to descend into the file any further if it is a directory.   
Hence, when find starts with the current directory, prune does not allow it to descend the current directory since it itself is a directory,   
and hence only the current directory gets printed, not the files within the directory.   
The print happens here because it is the default functionality of find to print anything which is true. 

删完之后，世界变得辽阔，神清气爽！
