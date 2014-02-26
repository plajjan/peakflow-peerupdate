peakflow-peerupdate
===================
This script helps you keep your peer definitions up to date in Peakflow. That
includes a Managed Object per peer and interface rules to match private network
interconnect interfaces.

It uses the pypeakflow library to communicate with Peakflow, so make sure you
have that.

Usage
-----
Common for all operations are that you need to specify the hostname or IP
address of your Peakflow system and a username and password with which to
authenticate. These options have been left out from the following examples for
clarity.

List 'peer' managed objects with:

	peakflow-peerupdate --list

Add a new managed object and associated interface rule with:

	peakflow-peerupdate --add --asn 1257 --name "AS1257 Tele2" --description "Tele2 international network" --precedence 100 --interface-desc-re "ABR:%(asn)s;"

This will create a managed object called "AS1257 Tele2" for peer-as 1257. An
interface ruled will be added that regexp matches interfaces with a description
of "ABR:AS1257;". '%(asn)s' in the interface description will be replace with
the content of the --asn option.

To provision a larger amount of peers, a text file can be used as input. The
format of the file is simple, one network per line with the AS number first
followed by the name;

	1239 SprintLink
	1257 Tele2
	1273 Cable & Wireless
	1299 Telia

Then run:

	peakflow-peerupdate --from-file input-data.txt --precedence 100 --interface-desc-re "ABR:%(asn)s;"

It will create one MO per network with the associated interface rule.
Precedence is based on the first network in the list and is increased by one
for the following networks, ie 1239 will receive a precedence of 100, Tele2
will have 101, Cable & Wireless will have 102 and so forth. Networks are
automatically sorted by AS number. Already existing rules for peers will be
renumbered according to the same rules. Other rules will be pushed down, ie if
you currently have a "catch all" rule at precedence 100 it will be pushed down
to 105.
