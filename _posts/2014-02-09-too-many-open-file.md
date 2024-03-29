---
layout: post
title: Too many open files
author: xluffy
date:   2014-02-09
tags: nginx, system, limit
description: 
---

If you are getting error “Too many open files (24)” then your application/command/script is hitting max open file limit allowed by linux. You need to increase open file limit as below:
Increase limit
Per-User Limit

Open file: /etc/security/limits.conf

Paste following towards end:

```
	*         hard    nofile      500000
	*         soft    nofile      500000
	root      hard    nofile      500000
	root      soft    nofile      500000
```

500000 is fair number. I am not sure what is max limit but 999999 (Six-9) worked for me once as far as I remember.

Once you save file, you may need to logout and login again.
pam-limits

I read at many places that an extra step is neede for limit to change for daemon processes. I did not need following yet, but if above changes are not working for you, you may give this a try.

Open /etc/pam.d/common-session

Add following line:

```
	session required pam_limits.so
```

System-Wide Limit

Set this higher than user-limit set above.

Open /etc/sysctl.conf 

Add following:

```
	fs.file-max = 2097152
```

Run:

`sysctl -p`

Above will increase “total” number of files that can remain open system-wide.
Verify New Limits

Use following command to see max limit of file descriptors:

`cat /proc/sys/fs/file-max`

Hard Limit

`ulimit -Hn`

Soft Limit

`ulimit -Sn`

if you are logged in as root:
Check limit for other user

Just replace www-data by linux username you wish to check limits for:

`su - www-data -c 'ulimit -aHS' -s '/bin/bash'`

Check limits of a running process:

Find process-id (PID):

`ps aux | grep process-name`

Suppose, XXX is PID, then run following commands to check limits:

`cat /proc/XXX/limits`

