# bash4-syslog-patch
-----------------------------------------------------
README

bash 4.4 patch for syslog and be able to show the login username, login ip address etc


With no patch(but with syslog enable in config-top.h) you can see in log: (with sudo -s ==> root):

Sep 20 20:12:49 -bash: HISTORY: PID=1722 UID=0 cat /var/log/syslog


With these patches, you can see:(with sudo -s ==> root):

Jun  8 11:04:04 ip-192-168-1-188 -bash[15245]: HISTORY: IP=123.444.666.33 PID=15245 PPID=15244 SID=15219 UID=0 USER=root LOGIN=ec2-user CMD=pwd


-------------------------------------------------------
wget http://ftp.gnu.org/gnu/bash/bash-4.4.tar.gz

tar -zxvf bash-4.4.tar.gz

cd bash-4.4

patch config-top.h <config-top.h.patch

patch bashhist.c <bashhist.c.patch

diff -Npru config-top.original.h config-top.h >config-top.h.patch

diff -Npru bashhist.original.c bashhist.c >bashhist.c.patch



./configure -prefix=/usr/local/bash_4.4

make

make install



echo '/usr/local/bash_4.4/bin/bash' >> /etc/shells

sed -i '/root/s#/bin/bash#/usr/local/bash_4.4/bin/bash#' /etc/passwd
