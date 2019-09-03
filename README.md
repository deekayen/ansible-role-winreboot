win_reboot
=========
[![Build Status](https://travis-ci.org/deekayen/ansible-role-winreboot.svg?branch=master)](https://travis-ci.org/deekayen/ansible-role-winreboot) [![Platforms](http://img.shields.io/badge/platforms-windows-lightgrey.svg?style=flat)](#)

This Ansible role lets you check for an optionally perform reboots on Windows nodes. During the reboot cycle it will use a local task on the control node to check when the node is back online.


Requirements
------------

N/A

Default Variables
--------------

Setting simulate to yes will force the role to think the node has a pending reboot.

`winreboot_simulate: no`

Reboot_behavior controls when to reboot the node. valid options are:
* never
* if_required
* always

`winreboot_reboot_behavior: if_required`

Maximum seconds to wait for a single successful TCP connection to the WinRM endpoint before trying again.

`winreboot_connect_timeout: 5`

Dependencies
------------

N/A

Example Playbook
----------------

    - hosts: platform_windows

      vars:
        winreboot_simulate: yes

      roles:
        - role: deekayen.win_reboot

    - hosts: all

      roles:
        - role: deekayen.win_reboot

License
-------

BSD
