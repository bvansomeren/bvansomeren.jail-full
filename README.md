bvansomeren.jail-full
=====================

Creates a single full jail

These jails tend to be long lived server replacement kind of jails and are setup to be used as such.  
They are easy to move as they do not depend on clones.
A base install of FreeBSD weighs in about 400mb after compression.

My previous roles allowed several jails in one go, this code proved fragile in the face of changing Ansible versions. Maybe they were trying to tell me something.
The role doesn't use any extra utilities or scripts and just relies on ZFS and jail.conf(5)  
Extra packages can be installed from the host as required, further configuration should be done using Ansible into the created jail (TODO: Add examples)  

This role makes extensive use of the /etc/jail.conf file.
You should run bvansomeren.jail-setup before running this role.
Because this role uses ansible blockinfile it will only change the blocks in the configuration, leaving the other ones alone.  
The idea is that you can have many playbooks which invoke their own jails to the same host without collision (except of course on name, IP and locations)  
You might even get away with jails installed manually or through other means...

Requirements
------------

None. FreeBSD with a zpool(1)

Role Variables
--------------


Dependencies
------------

No hard dependencies other than obviously FreeBSD (and less obviously ZFS. Design choice)  

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

````
- hosts: jumphost
  become: True
  vars:
    jail_ip: 10.0.0.125
    jail_name: jumpjail1
  roles:
  - role: bvansomeren.pf
    pf_localnet: "10.0.0.0/8"
    pf_jail_anchor: True
  - role: bvansomeren.jail-setup
    jail_default_interface: "vtnet1"
  - role: bvansomeren.jail-full
    hostname: ajumpjail.example.com
    setup_ssh: True
    rdr_ports:
    - 10022
    extra_opts:
    - name: ip4.addr
      option: "{{ jail_ip }}"
    jail_zfs_mounts:
    - name: logs
      extra_zfs_properties:
        mountpoint: "{{ jail_mountpoint }}/var/log"
        compression: gzip
        quota: 10G
````

License
-------

BSD

Author Information
------------------

Just reach out via Github
