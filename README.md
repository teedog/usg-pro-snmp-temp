# usg-pro-snmp-temp: Add temperature data to SNMP

A trivial patch that allows temperature data of the [USG-PRO-4](https://www.ubnt.com/unifi-routing/unifi-security-gateway-pro-4/) to be reported inside SNMP. This is the initial version, written on 10/02/2017. Involves manual modifications of your USG-PRO.

# Installation guide

## Prerequisites

* IP/Hostname of your USG-PRO (*$USG-PRO*)
* User to log into the USG-PRO (*$USERNAME*)

## Procedure

1. Clone this GIT

2. Copy the shell scripts <i>cpuTemp</i> and <i>phyTemp</i> to your USG-PRO

        $ scp cpuTemp phyTemp $USERNAME@$USG-PRO

3. Log into the USG-PRO by ssh and become root

        $ ssh $USERNAME@$USG-PRO
        $ sudo -s

4. Move the required shell scripts to /usr/local/bin

        $ mv ~$USERNAME/cpuTemp /usr/local/bin
        $ mv ~$USERNAME/phyTemp /usr/local/bin
        $ chown root:root /usr/local/bin/cpuTemp
        $ chown root:root /usr/local/bin/phyTemp
        $ chmod 755 /usr/local/bin/cpuTemp
        $ chmod 755 /usr/local/bin/phyTemp

5. Allow user snmp to fetch temperature data using ubnt-hal by adding the following line to /etc/sudoers

        $ echo "snmp     ALL = NOPASSWD: /usr/sbin/ubnt-hal" >> /etc/sudoers

6. Configure SNMPD to fetch temperature data

        $ echo "pass .1.3.6.1.4.1.4413.1.1.43.1.8.1.5.1.0 /usr/local/bin/cpuTemp" >> /etc/snmp/snmpd.conf
        $ echo "pass .1.3.6.1.4.1.4413.1.1.43.1.8.1.5.1.1 /usr/local/bin/phyTemp" >> /etc/snmp/snmpd.conf

7. Restart SNMPD

        $ pkill -HUP snmpd

# Testing

Use your favorite SNMP browser to test the new OIDs.

* OID .1.3.6.1.4.1.4413.1.1.43.1.8.1.5.1.0 should contain the cpu temperature
* OID .1.3.6.1.4.1.4413.1.1.43.1.8.1.5.1.1 should contain the physical temperature (chassis?)

