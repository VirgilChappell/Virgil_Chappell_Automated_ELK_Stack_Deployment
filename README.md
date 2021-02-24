# Virgil_Chappell_Automated_ELK_Stack_Deployment
 Elk Stack Project
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.  

https://drive.google.com/file/d/1M8hA93KYuBHn8XVoEG9IC-Vr0drhRxfO/view?usp=sharing

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the my_playbook.yml file may be used to install only certain pieces of it, such as Filebeat.

  - /etc/ansible/my_playbook.yml 

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting DDOS attacks to the network.
Load Balancers protect the availability of an application or network by spreading out web traffic which can also help prevent DDOS attacks. The advantage of a jump box is it allows an admin to access/monitor multiple servers from a secure location.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
-What does Filebeat watch for?_ Log files.
-What does Metricbeat record?_ Metrics and statistics.

The configuration details of each machine may be found below.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web-1    | Server   | 10.0.0.8   | Linux            |
| Web-2    | Server   | 10.0.0.11  | Linux            |
| Web-3    | Server   | 10.0.0.6   | Linux            |
| Elk      | Server   | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the ELK machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
-Whitelisted IP addresses_ 73.106.184.132 & 52.149.212.141

Machines within the network can only be accessed by the load balancer.
-Which machine did you allow to access your ELK VM? What was its IP address?_ The Jump Box VM. 52.149.212.141 

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 73.106.184.132       |
| Web-1    | No                  | 10.0.0.4             |
| Web-2    | No                  | 10.0.0.4             |
| Web-3    | No                  | 10.0.0.4             |
| ELK      | Yes                 | 52.149.212.141       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
-From a single playbook you can add commands to multiple srvers.

The playbook implements the following tasks:
- Install docker.io
- Install python3-pip
- Install docker module
- Increase virtual memory
- Use more memory
- Download and launch a docker elk container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

https://drive.google.com/file/d/1ZPElaN3TAASohc2wrZFkZ_B16Dfhl7gE/view?usp=sharing    


### Target Machines & Beats
This ELK server is configured to monitor the following machines:

Web-1 10.0.0.8
Web-2 10.0.0.11
Web-3 10.0.0.6

We have installed the following Beats on these machines:
-Filebeat and Metricbeat.

These Beats allow us to collect the following information from each machine:

Filebeats ships data about the file system. An example would be monitoring files for any suspicious changes. 
Metricbeats is a shipper that collects metrics and statistics from services running on the server. An example would be uptime.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- _Which file is the playbook? Where do you copy it?_ filebeat-7.4.0-amd64.deb and I copied it to /etc/ansible/roles
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_ /etc/ansible/hosts Because the ELK is listed seperately in the hosts file: 
[elkservers]
10.1.0.4 ansible_python_interpreter=/usr/bin/python3

_Which URL do you navigate to in order to check that the ELK server is running? 20.55.242.194:5601/app/kibana

_Provide the specific commands the user will need to run to download the playbook, update the files, etc._
Filebeat:
curl http://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/files/filebeat-config.yml
cd /etc/filebeat/filebeat-config.yml
nano filebeat-config.nano
Scroll to line #1106 and replace the IP address with the IP address of your ELK machine.
Scroll to line #1806 and replace the IP address with the IP address of your ELK machine.
Save this file in  /etc/ansible/files/filebeat-config.yml.
Download the .deb file from artifacts.elastic.co and run:
dpkg -i filebeat-7.4.0-amd64.deb
nano filebeat-playbook.yml
---
- name: Installing and Launch Filebeat
  hosts: webservers
  become: yes
  tasks:
    # Use command module
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb
    # Use command module
  - name: Install filebeat .deb
    command: dpkg -i filebeat-7.4.0-amd64.deb
    # Use copy module
  - name: Drop in filebeat.yml
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml
    # Use command module
  - name: Enable and Configure System Module
    command: filebeat modules enable system
    # Use command module
  - name: Setup filebeat
    command: filebeat setup
    # Use command module
  - name: Start filebeat service
    command: service filebeat start

    ansible-playbook filebeat-playbook.yml
