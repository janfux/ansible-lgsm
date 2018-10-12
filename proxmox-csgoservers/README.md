# Automatically set up csgoservers from proxmox container template

This project tries to automatically set up a number of csgoservers on a proxmox cluster.
It's meant to be used as a quick deployment method for game events/ LAN parties.

## Prerequisites

- Working proxmox server or cluster

- Proxmox ubuntu or debian container installed with lgsm csgoserver per https://linuxgsm.com/lgsm/csgoserver/, converted to template

- Ansible inventory file with hostnames, ip addresses, proxmox node and possible other info on csgoservers
    - Snippet:

    ````ini
    [csgoserver]
    csgoserver1 ansible_host=172.27.10.241 pve_node=pcs-gc-pve01
    csgoserver2 ansible_host=172.27.10.242 pve_node=pcs-gc-pve01
    csgoserver3 ansible_host=172.27.10.243 pve_node=pcs-gc-pve01
    ````

- Ansible variable file with info on proxmox host
  - Snippet:

  ````yaml
  
  ````

## Thoughts / WiP

Work in Progress ideas...


## 1. Create linked clone from template container

- best done with pvesh command on proxmox node, as ansible proxmox module does not support linked clones from containers (yet). See: https://github.com/ansible/ansible/issues/40921

- possible ansible solution from link:

    ````yaml
    - name: Get next VM ID
      register: nextid
      command: "pvesh get /cluster/nextid"

    - set_fact:
      newid: "{{ nextid.stdout | regex_replace('(\\W+)') | int }}"

    - name: Clone VM
      command: "pvesh create /nodes/{{ node }}/qemu/{{ vm_id }}/clone -newid {{ newid }} -name {{ name }}"
    ````
    
- for more pvesh commands like setting ip, add shh public key etc, see ``man pvesh`` on proxmox.

### 1a. Bootstrap for ansible

If the container template does not contain a python interpreter, at least package ``pythin-minimal`` has to be installed.

- Snippet:

    ````yaml
    - name: bootstrap host for ansible mgmt
      raw: test -e /usr/bin/python || (apt-get -y update && apt install -y python-minimal)
      changed_when: no
    ````
