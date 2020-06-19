#!/usr/bin/bash

#deleteUser - Automate the 4 steps to remove the account from the system

################################

function get_answer {

unset ANSWER
ASK_COUNT=0

while [ -z "$ANSWER" ] #WHILE no answer is given, keep asking
do
	ASK_COUNT=$[ ASK_COUNT + 1 ]

	case $ASK_COUNT in 
		2)
			echo
			echo "Please answer the question."
			echo
			;;
		3)
			echo
			echo "One last try ... please answer the question"
			echo
			;;
		4)
			echo
			echo "Since you refuse to answer the question... "
			echo "Exiting program"
			echo
			exit
			;;
	esac
	echo

	if [ -n "$LINE2" ]
	then
		echo $LINE1
		echo -e $LINE2" \c"
	else
		echo -e $LINE1" \c"
	fi

	#Allow 60 seconds to answer before time-out
	read -t 60 ANSWER
done

#little variable clean up
unset LINE1
unset LINE2

}

#################################
function process_answer {
	case $ANSWER in [yY]|[yY][eE][sS] )
		# if user answer yes, do nothing
		;;
	*)
		# if user answers anything but yes, exit script
		echo
		echo $EXIT_LINE1
		echo $EXIT_LINE2
		echo
		exit
		;;
esac

#little variable clean up

unset EXIT_LINE1
unset EXIT_LINE2
}

###################################

##########MAIN SCRIPT#############

# getting the name of the user account to check

echo "Step #1 - Determine User Account name to Delete"
echo
LINE1="Please enter the username of the user"
LINE2="account you wish to delete: "
get_answer
USER_ACCOUNT=$ANSWER

#

LINE1="Is $USER_ACCOUNT the user account"
LINE2="you wish to delete from the system? [y/n]"
get_answer

#calline process_answer function: if user answers anything but yes, exit script

EXIT_LINE1="Bescause the account, $USER_ACCOUNT, is not"
EXIT_LINE2="the one you wish to delete, we are exiting the script...."
process_answer

##################################################
#check if the user account exists on the system

USER_ACCOUNT_RECORD=$(cat /etc/passwd | grep -w $USER_ACCOUNT )

if [ $? -eq 1 ]
then
	echo
	echo "Account, $USER_ACCOUNT, not found. "
	echo "Leaving the Script... "
	echo
	exit
fi

echo
echo "I found this record:"
echo $USER_ACCOUNT_RECORD

LINE1="Is this the correct user account? [y/n]"
get_answer

# Calling process_answer : if user answers anything but yes, exit script

EXIT_LINE1="Bescause the account, $USER_ACCOUNT, is not"
EXIT_LINE2=" the on you wish to delete, exiting the script..."
process_answer

##############################################
# Searching for any running process

echo
echo "Step #2- Find the process on system belonging to user account"
echo

ps -u $USER_ACCOUNT > /dev/null

case $? in 
	1) # no process running 
		echo "There are no process running for the account currently: "
		echo
		;;
	0)
		echo "$USER_ACCOUNT has the following processes running: "
		echo
		ps -u $USER_ACCOUNT

		LINE1="Would you like to kill the process(es)? [y/n]"
		get_answer

		case $ANSWER in
			[yY]|[Yy][eE][sS] )

				echo
				echo "killing off process(es)..."

				COMMAND1="ps -u $USER_ACCOUNT --no-heading"

				COMMAND3="xargs -d \\n /usr/bin/sudo /bin/kill -9"

				$COMMAND1 | gawk '{print $1}' | $COMMAND3

				echo
				echo "Process(es) killed."
				;;
			*)
				echo 
				echo "Will not kill the process(es)"
				echo
				;;
		esac
		;;
esac
###################################################
# Create a report of all files owned by User Account

echo
echo "Step #3- Find files on system belonging to user account"
echo
echo " Creating a report of all files owned by $USER_ACCOUNT."
echo
echo "It is recommended that you backup/archive these files,"
echo "and then do one of two things:"
echo "	1) Delete the files"
echo "	2) Change the files' ownership to a current user account."
echo
echo "Please wait. This may take a while.. "

REPORT_DATE=$(date +%y%m%d)
REPORT_FILE=$USER_ACCOUNT"_Files_"$REPORT_DATE".rpt"

find ~$USER_ACCOUNT -user $USER_ACCOUNT > $REPORT_FILE 2>/dev/null

echo
echo "Report is complete."
echo "Name of report:		$REPORT_FILE"
echo "Location of report : 	$(pwd)"
echo

##############################################
# Remove User Account

echo
echo "Step #4 - Remove the user account"
echo

LINE1="Remove $USER_ACCOUNT's account from the system? [y/n]"
get_answer

# Calling process_answer: if user answers anything but "yes" , exit script

EXIT_LINE1="Since you do not wish to remove the user account,"
EXIT_LINE2="$USER_ACCOUNT at this time, exiting the scipt..."
process_answer

userdel $USER_ACCOUNT
echo
echo "User account, $USER_ACCOUNT, has been removed"
echo

exit

