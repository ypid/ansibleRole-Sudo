Sudo
=========

This role currently manages the sudoers file (not the sudoers.d).
Default behaviour is to construct the default clean OS sudoers file.

Ubuntu good start point:

	https://help.ubuntu.com/community/Sudoers


Role Variables
--------------

This role uses only one variable to construct the sudoers file, in general
the variable 'sudo_main_cfg' should be set in group_vars although it can
be used in host_vars as well.

sudo_main_cfg

The variable structure includes the sections below and can inclue multiple
entries per section or none except UserPriv that in general set to

'root       ALL=(ALL:ALL) ALL'.

	* Defaults
	* HostAlias
	* UserAlias
	* CmndAlias
	* UserPriv (REQUIRED)
	* RunasAlias


Example Playbook
----------------

Play on all nodes
```
- hosts: all
  gather_facts: False
  sudo: yes
  roles:
   - Sudo
```


Example group_vars/prod-example-org.yml defenition:

```
sudo_main_cfg:
 Defaults:
  - env_reset
  - editor=/usr/bin/vi
  - mail_badpass
  - 'secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"'
 UserAlias:
  - 'ADMINS = %admin,+admin'
  - 'USERS = tom, SpongeBob, harry'
  - 'WEBMASTERS = tom, mary'
  - 'LIMITED_USERS = USERS, !WEBMASTERS, !ADMINS'
 HostAlias:
  - 'SERVERS = 192.168.0.1, 192.168.0.2, server1'
 RunasAlias:
  - 'ROOT = #0'
  - 'ADMINS = %admin, root'
 CmndAlias:
  - 'SHUTDOWN_CMDS = /sbin/poweroff, /sbin/reboot, /sbin/halt'
  - 'PRINTING_CMDS = /usr/sbin/lpc, /usr/sbin/lprm'
  - 'ADMIN_CMDS = /usr/sbin/passwd, /usr/sbin/useradd, /usr/sbin/userdel, /usr/sbin/usermod, /usr/sbin/visudo'
  - 'WEB_CMDS = /etc/init.d/apache2'
 UserPriv:
  - 'root       ALL=(ALL:ALL) ALL'
  - 'ADMINS     ALL=(ALL) NOPASSWD: ALL'
  - 'WEBMASTERS webserver= WEB_CMDS'
  - 'USERS      WORKSTATIONS=(ADMINS) ADMIN_CMDS'
  - 'ALL        ALL=(ALL) NOPASSWD: PRINTING_CMDS'
```


The result file from the defenition above (without comments and additional info)

```
	Defaults        env_reset
	Defaults        editor=/usr/bin/vi
	Defaults        mail_badpass
	Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

	Host_Alias      SERVERS = 192.168.0.1, 192.168.0.2, server1

	User_Alias      ADMINS = %admin,+admin
	User_Alias      USERS = tom, SpongeBob, harry
	User_Alias      WEBMASTERS = tom, mary
	User_Alias      LIMITED_USERS = USERS, !WEBMASTERS, !ADMINS

	Cmnd_Alias      SHUTDOWN_CMDS = /sbin/poweroff, /sbin/reboot, /sbin/halt
	Cmnd_Alias      PRINTING_CMDS = /usr/sbin/lpc, /usr/sbin/lprm
	Cmnd_Alias      ADMIN_CMDS = /usr/sbin/passwd, /usr/sbin/useradd, /usr/sbin/userdel, /usr/sbin/usermod, /usr/sbin/visudo
	Cmnd_Alias      WEB_CMDS = /etc/init.d/apache2

	root       ALL=(ALL:ALL) ALL
	ADMINS     ALL=(ALL) NOPASSWD: ALL
	WEBMASTERS webserver= WEB_CMDS
	USERS      WORKSTATIONS=(ADMINS) ADMIN_CMDS
	ALL        ALL=(ALL) NOPASSWD: PRINTING_CMDS

	Runas_Alias     ROOT = #0
	Runas_Alias     ADMINS = %admin, root

	%admin          ALL=(ALL) ALL

	%sudo           ALL=(ALL:ALL) ALL
```


Author Information
------------------

Tal Lannder

Tal@Pjili.com
