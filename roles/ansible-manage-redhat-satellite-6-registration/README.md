# ansible-manage-redhat-satellite-6-registration

An ansible role that handles the registration and deregistration of machines from satellite server

Requirements
------------
= ansible 2.7

You need to have a working Red Hat Satellite 6 server in place with an activation key allowing you to register with Satellite 6. To be successful you need to add the following yum repos to the activationkey (others repos that might be needed can be added as well):

rhel-7-server-rpms
rhel-7-server-satellite-tools-6.2-rpms

Role Variables
--------------
A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

satellite_fqdn: xlab2358.aetnat.com
download_method: http
org: Default_Organization
location: Default Location
A_key: AC_rhel7_default
#hostgroup: AP_Test_Hosts
env: Dev
admin_user: Andy
admin_pass: "{{ vault_admin_pass }}"
role_action: Register
Desired_role_action: Deregister
host_state: absent	
pkg_state: absent	
ca_state: absent	
service_state: no	
katello_state: stopped	


Dependencies
------------
For the admin password you will need to generate a vault_admin_pass variable and place the variable
admin_pass: "{{ vault_admin_pass }}" in the vars/vars.yml file.
This tells ansible to go look for the encrypted vault_admin_pass variable in the encrypted vars/vault file.

If you are running this from a roles/ directory specify the path to the vars and vault file in your playbook calling the manage-redhat-satellite-6-registration role.

  vars_files:
    - ./roles/ansible-manage-redhat-satellite-6-registration/vars/vault.yml
    - ./roles/ansible-manage-redhat-satellite-6-registration/vars/vars.yml

Example Playbook
----------------
To run this playbook, you have to do the following:
(1) Create a directory called "roles" from the linux command line
(2) Change directory to the newly created "roles" directory
(3) Clone the project while in "roles" directory and you should be able to execute the host registration to the satellite server as well as the deregistration.

host_registration.yml
---
- hosts: test
  become: yes
  gather_facts: False
  vars_files:
    - ./roles/ansible-manage-redhat-satellite-6-registration/vars/vault.yml
    - ./roles/ansible-manage-redhat-satellite-6-registration/vars/vars.yml
  roles:
    -ansible-manage-redhat-satellite-6-registration

deregistration.yml
---
- hosts: test
  become: yes
  tasks:
    - name: Include ansible-manage-redhat-satellite-6-registration role
      include_role:
        name: ansible-manage-redhat-satellite-6-registration
      vars:
        desired_role_action: Deregister
        host_state: absent
        pkg_state: absent
        ca_state: absent
        service_state: no
        katello_state: stopped

License
-------

Aetna Proprietary

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).

