win_reboot
=========
[![CI](https://github.com/deekayen/ansible-role-winreboot/actions/workflows/ci.yml/badge.svg)](https://github.com/deekayen/ansible-role-winreboot/actions/workflows/ci.yml) [![Platforms](http://img.shields.io/badge/platforms-windows-lightgrey.svg?style=flat)](#) [![Project Status: Inactive – The project has reached a stable, usable state but is no longer being actively developed; support/maintenance will be provided as time allows.](https://www.repostatus.org/badges/latest/inactive.svg)](https://www.repostatus.org/#inactive)

This Ansible role lets you check for an optionally perform reboots on Windows nodes. During the reboot cycle it will use a local task on the control node to check when the node is back online.


Requirements
------------

N/A

Default Variables
--------------

Setting simulate to true runs the reboot in check mode, so nothing reboots.

`winreboot_simulate: false`

Reboot_behavior controls when to reboot the node. valid options are:
* never
* if_required
* always

`winreboot_reboot_behavior: if_required`

Maximum seconds to wait for a single successful TCP connection to the WinRM endpoint before trying again.

`winreboot_connect_timeout: 5`

Maximum seconds to wait for machine to re-appear on the network and respond to a test command.

`winreboot_reboot_timeout: 3600`

Dependencies
------------

N/A

Example Playbook
----------------

    - hosts: platform_windows

      vars:
        winreboot_simulate: true

      roles:
        - role: deekayen.win_reboot

    - hosts: all

      roles:
        - role: deekayen.win_reboot

License
-------

BSD
