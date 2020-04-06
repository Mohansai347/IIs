# IIs
---
- name: playbook for create a  user
  hosts: windows
  tasks:
     - name: Install IIS
      win_feature:
        name: Web-Server
        state: present
        restart: no
        include_sub_features: yes
        include_management_tool: yes


    - name: Ensure user IIS is created and use custom credentials to create the user
      win_domain_user:
        name: IIs_admin
        password: Test@12345
        state: present
        domain_username: vms\node2
        domain_password: VMware@123456
        domain_server: node2.vms.com

    - name: allow remote access to IIS
      win_group_membership:
        name: Remote Desktop Users
        members:
             - vms\IIs_admin
        state: present

    - name: creating file
      win_file:
        path: C:\inetpub\wwwroot
        state: directory

    - name: copying from master to node
      win_copy:
        src: /etc/ansible/activity/source
        dest: C:\Inetpub\wwwroot\

    - name: Set the owner of root directory
      win_owner:
        path: C:\Inetpub\wwwroot\
        user: vms\IIs_admin
        recurse: yes
