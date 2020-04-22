#!/bin/bash
EMAIL=''
function sysstat {

echo -e "---------------------------------CENTRALIZED SERVER AVAILABILITY CHECK--------------------------------"

ping  -c 2  -w 5  `ifconfig  | tail -8 | head -1 | tr -s " " | cut -d " " -f3`
if [ $? -eq 0 ]
then
echo "   "
echo "Server Status: Available"
else echo "Server Status: Unavailable"
fi
echo -e "
Server Name : `hostname`
Date & Time : `date`
Uptime      : `uptime -p`"
echo " "
echo -e "Overall Health Status:
Load Avetage : `uptime | grep -o 'load.*'`
SNMP Status  :`service snapd status | grep Active`
CPU Usage    : `top  -b -c  | head -n 5 | tail -3 | grep Cpu`
Memory Usage : `top  -b -c  | head -n 5 | tail -2 | head -1`
Swap Usage   : `top  -b -c  | head -n 5 | tail -2 | head -1`"
}

FILENAME="health-`hostname`-`date +%y%m%d`-`date +%H%M`.txt"
sysstat > $FILENAME
echo -e "Reported file $FILENAME generated in current directory." $RESULT
if [ "$EMAIL" != '' ]
then
        STATUS=`which mail`
        if [ "$?" != 0 ]
        then
                echo "The program 'mail' is currently not installed."
        else
                cat $FILENAME | mail -s "$FILENAME" $EMAIL
        fi
fi
