appservertomcat.yml
--------------------------------
code:   
---
- hosts: Dev
  connection: ssh

  tasks:
    - name: install java as dependency
      command: amazon-linux-extras install java-openjdk11 -y
    - name: download repo from dlcdn
      get_url:
        url: "https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.91/bin/apache-tomcat-9.0.91.tar.gz"
        dest: "/root"

    - name: untar the downloaded repo
      command: tar -zxvf apache-tomcat-9.0.91.tar.gz

    - name: rename the tar file
      command: mv apache-tomcat-9.0.91 tomcat
        #tags: abc // command ansible-playbook filename --skip-tags "abc"

    - name: replace context.xml
      copy:
        src: "context.xml"
        dest: "/root/tomcat/webapps/manager/META-INF/context.xml"

    - name: replace tomcat-user.xml file
      copy:
        src: "tomcat-users.xml"
        dest: "/root/tomcat/conf/tomcat-users.xml"

    - name: stop the tomcat
      shell: nohup /root/tomcat/bin/shutdown.sh


    - name: start the tomcat
      shell: nohup /root/tomcat/bin/startup.sh
*********************************************************************
code.yml
------------------------------
---
- hosts: Dev
  connection: ssh

  tasks:
    - name: get code from github
      git:
        repo: "https://github.com/Radha-4119/foodapplication.git"
        version: "main"
        dest: "my-code"

#private:
#- hosts: Prod
#  connection:ssh
#  tasks:
#  - name: getting code from private repo
#  git:
#  repo: "https://token@github.com/devops/reponame"
#  version: "main"
#  dest: "mustafa"
**********************************************************************
condition.yml
------------------
---
- hosts: Prod
  connection: ssh

  tasks:
    - name: install httpd on RedHat
      yum: name=httpd state=present
      when: ansible_os_family == "RedHat"

    - name: install apache2 on Ubuntu
      apt: name=apache2 state=present
      when: ansible_os_family == "Ubuntu"
****************************************************************************      
groups.yml
---------------
---
- hosts: Dev
  connection: ssh

  tasks:
    - name: adding user to slave server
      group: name={{item}} state=present
      with_items:
        - tcs
        - infosys
        - hcl
  ***********************************************************************
jenkins.yml
--------------
---
- hosts: localhost

  tasks:
  - name: install java as dependency
    command: amazon-linux-extras install java-openjdk11 -y

  - name: getting repo from jenkins.io
    get_url:
      url: "https://pkg.jenkins.io/redhat-stable/jenkins.repo"
      dest: "/etc/yum.repos.d/jenkins.repo"

  - name: getting key from jenkins.io
    ansible.builtin.rpm_key:
      key: "https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key"
      state: present

  - name: install jenkins
    yum: name=jenkins state=present

  - name: start jenkins
    service: name=jenkins state=started
**************************************************************************************
loop.yml
------------
---
- hosts: Dev
  connection: ssh

  tasks:
    - name: install multiple tasks using loops
      yum: name={{item}} state=present
      with_items:
        - git
        - tree
        - httpd
*******************************************************************************************
mulipleuser.yml
----------------
---
- hosts: Dev
  connection: ssh

  tasks:
    - name: creating multiple users to slave server
      user: name={{item}} state=present
      with_items:
        - aws
        - devops
        - httpd
********************************************************************************************
user.yml
----------
---
- hosts: Dev
  connection: ssh

  tasks:
    - name: adding user to slave server
      user: name=frontlinemedia state=present
********************************************************************************************
