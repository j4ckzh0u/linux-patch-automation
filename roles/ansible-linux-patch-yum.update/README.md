Role Name
=========
This role updates the linux system (RedHat) as follows:
1- It runs pre-checks like OS checks, and gather all the mandatory checks information(available diskspace on some mounted FS,mount,ip details,services,etc).
2- A threshold is set for some mount points that are used up during system patch like /,/var,/boot and /tmp. If the set threshold passes, patch the server but if not, it will fail the play and abort the patching of the server and notification email is generated and sent to the team/individual that oversees the patching of the server.
3- If patch succeeds the server is rebooted.
4- wait for server booting, once back and validate the server with the precheck data.


Requirements
------------
These playbooks were designed to be used in Linux system (RHEL and maybe CentOS). You need to have a valid yum repository on each managed node.


Role Variables
--------------
The variables that can be passed to this role are as follows:

supported_distros:  
  - RedHat
  - CentOS
local_dir is a directory where to store the logs during the time the playbook is running. Default: /tmp/ansible/sys-patching:
local_dir: "/tmp/ansible/sys-patching"
NB: The logs generated can be delegated to the ansible controller or the managed nodes.

supported_distros:
  - RedHat
  - CentOS

ip_address: "{{ ansible_default_ipv4.address }}"
Patching_type: SYSTEM_PATCH --> Full patch with system reboot (with or without kernel update - unconditional reboot)
vastool_status: "vas_status.stdout.split('\n')[4].split(':')"
vastool_path: "/opt/quest/bin/vastool"


*REBOOT VARIABLES*
server_update_reboot_connect_timeout: 5
server_update_reboot_pre_reboot_delay: 0
server_update_reboot_post_reboot_delay: 180
server_update_reboot_reboot_timeout: 600

-wait 5 seconds for a successful connection to the managed hosts before trying again
-Attempt to connect via ssh
-Disconnect after 5 seconds if it ssh isn’t working
-Keep attempting to connect for 10 minutes (600 seconds)
-Wait 180 seconds(3minutes) for services to start up and become available after reboot before post checks kicks in
NB: These timings can be changed to suit your need

*YUM UPDATE*
#For Redhat: List of packages to not update (comma separated).
#Can include wildcard to match multiple packages.
#server_update_yum_exclude_pkgs: "mysql*, bash, openssh*"
server_update_yum_exclude_pkgs: []
#
#For Redhat: List of packages to ONLY update (comma separated).
#Can include wildcard to match multiple packages.
#This will not update anything else (besides dependencies). '*' will update all installed packages.
#server_update_yum_install_pkgs: "kernel-*, iwl*firmwar"
server_update_yum_install_pkgs: '*'

Example Playbook
----------------
---
- name: patching the linux systems using patch-automation roles
  hosts: dev
  gather_facts: True
  remote_user: toweragent
  become: yes
  vars:
    patching_type: "SYSTEM_UPDATE"
  roles:
    - ansible-linux-patch-automation

Expected Result
---------------

"HOSTNAME: xlab2359.aetnat.com version before the update:"
"      distribution: RedHat "
"      distribution_major_version: 7"
"      distribution_release: Maipo"
"      distribution_version: 7.7"

"HOSTNAME: xlab2359.aetnat.com version after the update:"
"      distribution: RedHat "
"      distribution_major_version: 7"
"      distribution_release: Maipo"
"      distribution_version: 7.7"

HOSTNAME: xlab2359.aetnat.com | rngd.service : OK
HOSTNAME: xlab2359.aetnat.com | vasd.service : OK
HOSTNAME: xlab2359.aetnat.com | atd.service : OK
HOSTNAME: xlab2359.aetnat.com | getty@tty1.service : OK
HOSTNAME: xlab2359.aetnat.com | nscd.service : OK
HOSTNAME: xlab2359.aetnat.com | vgauthd.service : OK
HOSTNAME: xlab2359.aetnat.com | libstoragemgmt.service : OK
HOSTNAME: xlab2359.aetnat.com | network : OK
HOSTNAME: xlab2359.aetnat.com | vmtoolsd.service : OK
HOSTNAME: xlab2359.aetnat.com | rsyslog.service : OK
HOSTNAME: xlab2359.aetnat.com | tuned.service : OK
HOSTNAME: xlab2359.aetnat.com | goferd.service : OK
HOSTNAME: xlab2359.aetnat.com | abrt-oops.service : OK
HOSTNAME: xlab2359.aetnat.com | dbus.service : OK
HOSTNAME: xlab2359.aetnat.com | systemd-logind.service : OK
HOSTNAME: xlab2359.aetnat.com | lvm2-lvmetad.service : OK
HOSTNAME: xlab2359.aetnat.com | rhsmcertd.service : OK
HOSTNAME: xlab2359.aetnat.com | crond.service : OK
HOSTNAME: xlab2359.aetnat.com | abrtd.service : OK
HOSTNAME: xlab2359.aetnat.com | gssproxy.service : OK
HOSTNAME: xlab2359.aetnat.com | polkit.service : OK
HOSTNAME: xlab2359.aetnat.com | chronyd.service : OK
HOSTNAME: xlab2359.aetnat.com | xinetd.service : OK
HOSTNAME: xlab2359.aetnat.com | postfix.service : OK
HOSTNAME: xlab2359.aetnat.com | systemd-udevd.service : OK
HOSTNAME: xlab2359.aetnat.com | sshd.service : OK
HOSTNAME: xlab2359.aetnat.com | systemd-journald.service : OK
HOSTNAME: xlab2359.aetnat.com | irqbalance.service : OK
HOSTNAME: xlab2359.aetnat.com | /dev/mapper/rootvg-root      8.0G  2.2G  5.9G  28% / : MOUNTED
HOSTNAME: xlab2359.aetnat.com | /dev/sda1                   1014M  214M  801M  22% /boot : MOUNTED
HOSTNAME: xlab2359.aetnat.com | /dev/mapper/rootvg-var       8.0G  3.2G  4.9G  39% /var : MOUNTED
HOSTNAME: xlab2359.aetnat.com | /dev/mapper/rootvg-home      8.0G  449M  7.6G   6% /home : MOUNTED
HOSTNAME: xlab2359.aetnat.com | /dev/mapper/uservg-u01        20G   33M   20G   1% /u01 : MOUNTED
HOSTNAME: xlab2359.aetnat.com | /dev/mapper/rootvg-opt       8.0G   70M  8.0G   1% /opt : MOUNTED
HOSTNAME: xlab2359.aetnat.com | /dev/mapper/rootvg-tmp       8.0G   34M  8.0G   1% /tmp : MOUNTED
HOSTNAME: xlab2359.aetnat.com | /dev/mapper/rootvg-midrange  8.0G  331M  7.7G   5% /midrange : MOUNTED
HOSTNAME: xlab2359.aetnat.com | interface eth0 : OK
HOSTNAME: xlab2359.aetnat.com | interface eth0 : 10.80.148.147 : OK
HOSTNAME: xlab2359.aetnat.com | SELinux: OK

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
