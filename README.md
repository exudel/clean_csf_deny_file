# clean_csf_deny_file

#!/bin/bash

#########################################################
# You can use this script and set crontab to delete all 
# IP address which are blocked by CSF firewall. With	
# this scripts you can avoid to block regular user for 	
# a long time "because they bad typing password on		
# server more times".									
# v0.1							 						
#########################################################

DIRECTORY=/home/root/csf/
DATE=`date +%Y-%m-%H-%M`

if [ -d "$DIRECTORY" ]; then

	cat /etc/csf/csf.deny > /home/root/csf/csf.deny-$DATE
	FILE=/home/root/csf/csf.deny-$DATE
	LOG=/home/root/csf/csf.deny-log

        if [ -s "$FILE" ]
       	then
                systemctl stop csf > /dev/null 2>&1
               	sleep 5s
                sed -i '/[0-9]$/d' /etc/csf/csf.deny
                echo "IP addresses are deleted from csf.deny "$DATE >> $LOG
                find "$DIRECTORY" -cmin +1440 -name 'csf.deny*' -delete
                systemctl start csf > /dev/null 2>&1
       	else
                echo "copy of csf.deny are not exists"$DATE >> $LOG
       	fi
else
        mkdir /home/root/csf
        touch /home/root/csf/csf.deny-log
		find . -type f -name "clean_csf_deny.sh" -exec ./clean_csf_deny.sh {} \;
fi
