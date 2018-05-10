# bash4-syslog-patch
-----------------------------------------------------
README

bash 4.4 patch for syslog and be able to show the login username, login ip address etc


With no patch(but with syslog enable in config-top.h) you can see in log: (with sudo -s ==> root):

Sep 20 20:12:49 -bash: HISTORY: PID=1722 UID=0 cat /var/log/syslog


With these patches, you can see:(with sudo -s ==> root):

Jun  8 11:04:04 ip-192-168-1-188 -bash[15245]: HISTORY: IP=123.444.666.33 PID=15245 PPID=15244 SID=15219 UID=0 USER=root LOGIN=ec2-user CMD=pwd


-------------------------------------------------------

bash --version

yum install -y wget gcc patch

wget http://ftp.gnu.org/gnu/bash/bash-4.4.tar.gz

tar -zxvf bash-4.4.tar.gz

cd bash-4.4

cp config-top.h config-top.original.h

cp bashhist.c bashhist.original.c

wget https://raw.githubusercontent.com/debu99/bash4-syslog-patch/master/bashhist.c.patch

wget https://raw.githubusercontent.com/debu99/bash4-syslog-patch/master/config-top.h.patch

patch config-top.h <config-top.h.patch

patch bashhist.c <bashhist.c.patch

diff -Npru config-top.original.h config-top.h >config-top.h.patchlog

diff -Npru bashhist.original.c bashhist.c >bashhist.c.patchlog

cat bashhist.c.patchlog

cat config-top.h.patchlog

./configure -prefix=/usr/local/bash_4.4

make

make install



echo '/usr/local/bash_4.4/bin/bash' >> /etc/shells

cat /etc/passwd|grep /bin/bash

sed -i 's#/bin/bash#/usr/local/bash_4.4/bin/bash#g' /etc/passwd


-------------------------------------------------------
If you like you can also change these below:

And make the next few lines:

#if defined (SYSLOG_HISTORY)
# define SYSLOG_FACILITY LOG_USER
# define SYSLOG_LEVEL LOG_INFO
#endif

Change to:

#if defined (SYSLOG_HISTORY)
# define SYSLOG_FACILITY LOG_LOCAL1
# define SYSLOG_LEVEL LOG_DEBUG
#endif

LOG_USER and LOG_INFO means that bash history will use the “user” facility and “info” level. By default, if we don’t change the facility and level of syslog, the bash history will fill with the /var/log/messages …

Available facility: kern, user, info, mail, daemon, auth, syslog, news, uucp, lpr, ftp, cron, local0-7

Available level: emerg, alert, crit, err, notice, info, debug

So I choose the unusual facility “local1″ and level “debug”.

The bash history has the same effect like this command: logger -p local1.debug “helloworld” .
