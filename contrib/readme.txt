S3FS-Fuse

S3FS is FUSE (File System in User Space) based solution to mount/unmount an Amazon S3 storage buckets and use system commands with S3 just like it was another Hard Disk.

In order to compile s3fs, You'll need the following requirements:

* Kernel-devel packages (or kernel source) installed that is the SAME version of your running kernel
* LibXML2-devel packages
* CURL-devel packages (or compile curl from sources at: curl.haxx.se/ use 7.15.X)
* GCC, GCC-C++
* pkgconfig
* FUSE (2.7.x)
* FUSE Kernel module installed and running (RHEL 4.x/CentOS 4.x users - read below)
* OpenSSL-devel (0.9.8)
* Subversion

If you're using YUM or APT to install those packages, then it might require additional packaging, allow it to be installed.

Downloading & Compiling:
------------------------
In order to download s3fs, user the following command:
svn checkout http://s3fs.googlecode.com/svn/trunk/ s3fs-read-only

Go inside the directory that has been created (s3fs-read-only/s3fs) and run: make
This should compile the code. If everything goes OK, you'll be greated with "ok!" at the end and you'll have a binary file called "s3fs"

As root (you can use su, su -, sudo) do: "make inatall" -this will copy the "s3fs" binary to /usr/bin.

Congratulations. S3fs is now compiled and Installed.

Usage:
------
In order to use s3fs, make sure you have the Access Key and the Secret Key handy.
First, create a directory where to mount the S3 bucket you want to use.
Example (as root): mkdir -p /mnt/s3
Then run: s3fs mybucket -o accessKeyId=aaa -o secretAccessKey=bbb /mnt/s3

This will mount your bucket to /mnt/s3. You can do a simple "ls -l /mnt/s3" to see the content of your bucket.

If you want to allow other people access the same bucket in the same machine, you can add "-o allow _other" to read/write/delete content of the bucket.

You can add a fixed mount point in /etc/fstab, here's an example:

s3fs#mybucket /mnt/s3 fuse allow_other,accessKeyId=XXX ,secretAccessKey=YYY 0 0

This will mount upon reboot (or by launching: mount -a) your bucket on your machine.

All other options can be read at: http://code.google.com/p/s3fs/wiki/FuseOverAmazon

Known Issues:
-------------
s3fs should be working fine with S3 storage. However, There are couple of limitations:

* There is no full UID/GID support yet, everything looks as "root" and if you allow others to access the bucket, others can erase files. There is, however, permissions support built in.
* Currently s3fs could hang the CPU if you have lots of time-outs. This is *NOT* a fault of s3fs but rather libcurl. This happends when you try to copy thousands of files in 1 session, it doesn't happend when you upload hundreds of files or less.
* CentOS 4.x/RHEL 4.x users - if you use the kernel that shipped with your distribution and didn't upgrade to the latest kernel RedHat/CentOS gives, you might have a problem loading the "fuse" kernel. Please upgrade to the latest kernel (2.6.16 or above) and make sure "fuse" kernel module is compiled and loadable since FUSE requires this kernel module and s3fs requires it as well.
* Moving/renaming/erasing files takes time since the whole file needs to be accessed first. A workaround could be touse s3fs's cache support with the use_cache option.

