# Configure Docker using Ansible Playbook on Linux Servers

This guide provides instructions on how to configure Docker on multiple Linux servers using an Ansible playbook.

## Prerequisites

1. **Ansible**: Ensure Ansible is installed on your control machine.
2. **Inventory File**: Create an inventory file listing your target servers.
3. **Playbook**: Create an Ansible playbook to automate the Docker installation and configuration.

## Inventory File

Create an inventory file (`ansible/inventory/inventory.ini`) with the following content:

```
[rke_servers]
server1 ansible_host=<hidden> ansible_user=<hidden> ansible_password=<hidden>
server2 ansible_host=<hidden> ansible_user=<hidden> ansible_password=<hidden>
server3 ansible_host=<hidden> ansible_user=<hidden> ansible_password=<hidden>
server4 ansible_host=<hidden> ansible_user=<hidden> ansible_password=<hidden>
server5 ansible_host=<hidden> ansible_user=<hidden> ansible_password=<hidden>
server6 ansible_host=<hidden> ansible_user=<hidden> ansible_password=<hidden>
```




## Playbook

Create a playbook (`ansible/playbooks/docker/docker.yml`) with the following content:

```
- name: Install Docker and configure systctl
  hosts: rke_servers
  become: yes
  tasks:
    - name: Disable swap
      command: swapoff -a
      become: true

    - name: Remove swap entry from /etc/fstab
      lineinfile:
        path: /etc/fstab
        regexp: 'swap'
        state: absent
      become: true

    - name: Add Kubernetes sysctl settings
      blockinfile:
        path: /etc/sysctl.d/kubernetes.conf
        create: yes
        block: |
          net.bridge.bridge-nf-call-ip6tables = 1
          net.bridge.bridge-nf-call-iptables = 1
      become: true

    - name: Apply sysctl settings
      command: sysctl --system
      become: true

    - name: Disable firewalld
      systemd:
        name: firewalld
        state: stopped
        enabled: no
      become: true
      ignore_errors: yes

    - name: Disable SELinux
      command: setenforce 0
      become: true

    - name: Ensure SELinux is disabled in config file
      replace:
        path: /etc/selinux/config
        regexp: '^SELINUX=enforcing'
        replace: 'SELINUX=disabled'
      become: true

    - name: Install required packages
      yum:
        name: 
          - yum-utils
          - device-mapper-persistent-data
          - lvm2
        state: present

    - name: Add Docker repository
      shell: |
        yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

    - name: Install Docker
      shell: |
        sudo yum install -y docker-ce-26* docker-ce-cli-26* containerd.io docker-buildx-plugin docker-compose-plugin
      tags:
        docker

    - name: Start and enable Docker
      systemd:
        name: docker
        state: started
        enabled: yes

    - name: Add cloud-user to docker group
      user:
        name: cloud-user
        groups: docker
        append: yes

    - name: Verify Docker is running
      command: docker --version
      register: docker_version

    - debug:
        msg: "Docker version installed: {{ docker_version.stdout }}"

## Applying the Playbook

Run the following command to apply the playbook:
