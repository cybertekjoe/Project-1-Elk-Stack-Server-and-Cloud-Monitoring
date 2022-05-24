## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Alt Text](https://github.com/cybertekjoe/Project-1/blob/main/Diagrams/Unit12-Homework-NetworkDiagram.drawio.png "Network Diagram")

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook___ file may be used to install only certain pieces of it, such as Filebeat.

 The Elk Playbook
```
---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: sysadmin
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

      # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes
```
This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly __secure__, in addition to restricting __access__ to the network.
What aspect of security do load balancers protect?
__The off-loading function of a load balancer defends an organization against distributed denial-of-service (DDoS) attacks. It does this by shifting attack traffic from the corporate server to a public cloud provider.__ _(Source https://avinetworks.com/what-is-load-balancing/)_
What is the advantage of a jump box?
_the jump box has only one path in via SSH, and no other protocols are allowed outbound to the Internet or into the corporate network._ (Source: https://www.linux-magazine.com/Online/Features/Jump-Box-Security)

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the __data__ and system __logs__.
- What does Filebeat watch for?
_Filebeat monitors the log files_
- What does Metricbeat record?
_Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash._ (Source: https://www.elastic.co)

The configuration details of each machine may be found below.

| Name       | Function        | Ip Address | Operating System     |
|------------|-----------------|------------|----------------------|
| Jump Box   | Gateway         | 10.0.0.4   | Linux (ubuntu 20.04) |
| Elk-Server | Virtual Machine | 10.1.0.4   | Linux (ubuntu 20.04) |
| Web-1      | Virtual Machine | 10.0.0.5   | Linux (ubuntu 20.04) |
| Web-2      | Virtual Machine | 10.0.0.6   | Linux (ubuntu 20.04) |
| Web-3      | Virtual Machine | 10.0.0.6   | Linux (ubuntu 20.04) |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the __Jumpbox__ machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- _20.92.124.7_ (This is the public ip of your jumpbox)
- _10.0.0.4_ (This is the private ip of your jumpbox

Machines within the network can only be accessed by _SSH_.
- Which machine did you allow to access your ELK VM?
-- _Only the Local computer_
- What was its IP address? _50.115.241.43_ (This is the public ip of your local computer)

A summary of the access policies in place can be found in the table below.

|      Name     | Publicly Accessible |   Allowed IP Addresses   |
|:-------------:|:-------------------:|:------------------------:|
| Jump Box      | No                  | 20.53.224.18 on SSH 22   |
| Elk Server    | No                  | 52.189.197.156 on SSH 22 |
|               |                     |     on port 5601         |
| Load Balancer | No                  | 20.213.37.243 on Http 80 |
| Web-1         | No                  | 10.0.0.5 on SSH 22       |
| Web-2         | No                  | 10.0.0.6 on SSH 22       |
| Web-3         | No                  | 10.0.0.7 on SSH 22       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- The main advantage of automating configuration with Ansible is to be able to configure multiple machines with only one playbook

The playbook implements the following tasks:
- Install docker.io, pip3, and python modules
- Increase the memory of the vm
- Download and launch a docker elk container
- Establish accessible ports
- Enable docker servie on boot

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Alt Text](https://github.com/cybertekjoe/Project-1/blob/main/Images/Docker_ps_Results.png "Docker ps Output")

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1 : 10.0.0.4
- Web-2 : 10.0.0.5
- Web-3 : 10.0.0.6
- 
We have installed the following Beats on these machines:
- ELK Server, Web-1, Web-2, and Web-3
- The ELK Stack Installed are: FileBeat and MetricBeat

These Beats allow us to collect the following information from each machine:
- Filebeat: log events and forwards them to Elasticsearch for indexing
- Metricbeat: metrics and statistics that it collects and ships them to the output that you specify like Elasticsearch

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the __[Install-Elk.yml](https://github.com/cybertekjoe/Project-1/blob/main/Ansible/install-elk.yml)__ file to __the Ansible folder__.

- Update the __hosts__ file to include this statement under the webersver section
  - [elk]
  - 10.1.0.4 ansible_python_interpreter=/usr/bin/python3

- copy the filebeat 

- Run the playbook, and navigate to http://(your-elk-server-ip):5601/app/kibana to check that the installation worked as expected.

Copy the Ansible ELK Installation and VM Configuration
Run the playbook using this command : ansible-playbook install-elk.yml
  - The file is called install-elk.yml
  - this file is copied to the ansible server in the /etc/ansible/ directory
  - Update the hosts file in the ansible folder to make the playbook run in ansible
  - Change the  How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._


