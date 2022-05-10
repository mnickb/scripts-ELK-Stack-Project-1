# scripts-ELK-Stack-Project-1
## Automated-ELK-Stack-Deployment
### Project 1
Files used in this repository were used to configure the ELK Stack network

In this project, I am acting as a DevOps professional that’s tasked to set up a cloud monitoring system by configuring an ELK stack server.  The ELK monitoring stack is deployed within the virtual networks.  The picture below depicts the architecture diagram used to configure the network.


 
https://drive.google.com/file/d/142A8ctEyjr3NGk3ttGuK5KVwSOMnd4iR/view?usp=sharing


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the FileBeat Configuration file may be used to install only certain pieces of it, such as Filebeat.


The ELK stack playbook.
 https://drive.google.com/file/d/1s0bzpULj-PE5r6_nC4vDkbTmXCYEFAQc/view?usp=sharing

https://drive.google.com/file/d/1COBzSLI3bIub-K1xrAC-dVQc5to5EPk4/view?usp=sharing


This Project contains the following details:
 - Description of the Topology
 - Access Policies
 - ELK Configuration
    - Beats in Use
    - Machines Being Monitored
  - How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damn Vulnerable Web Application.
Load balancing ensures that the application will be highly responsive, in addition to restricting traffic to the network.

 - What aspect of security do load balancers protect? 

The load balancers protect against distributed denial-of-service (DDOS) attacks by spreading the workload over multiple servers improving the performance and availability of websites, applications, databases, and other resources.  In this project my load balancer is named RedTeamLB.  I added a fronted IP configuration with an IP version set at IPv4 and public IP address set with basic sku and static assignment.  Also added a backend pool, RedTeamPool, associated to all virtual machines (Web-1 and Web-2) and an IP version set at IPv4. Then added the health probe, RedTeamProbe, tied to port 80. The health probes to detect the failure of an application and probes responses to determines which backend pool instances will receive new connections. I added a new inbound security rule to RedTeamSG, using my public IP address (69.255.217.151) as the source IP address. Also added a new load balancing rule, PenTestLBR, adding the frontend IP address, backend pool, and health probe.  The Elk-Server VM is indirectly connected to the load balancer through Web-1 and Web-2. 
 
 - What is the advantage of a jump box?

The jump box is the gatekeeper/origination point that ensures that only the authorized users must connect to before performing any administration tasks and that tracks all user activity.  My ElK-Server is not accessible from outside the virtual machine network.  Instead, it is accessed by SSH through my JumpBoxProvisioner.  Also, my network security group, RedTeamSG,is configured to allow TCP type traffic from the public internet on port 22 to allow access through SSH. 


Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.

 - What does Filebeat watch for?

Filebeat watch for forwarding and centralizing log data such as server logs, gc logs, depreciation logs, slow logs, and audit logs.

 - What does Metricbeat record?

Metricbeat records the behavior and usage of the system resources that can be collected and monitored from the system such as CPU, memory, and other data related to services running on the server.


The configuration details of each machine may be found below.


| Name       | Function               |IP Address   |Operating System |
|------------|------------------------|-------------|-----------------|
| Jump Box   | Gateway                | 10.0.0.4    | Linux           |
| Web-1      | Configure VM           | 10.0.0.5    | Linux           |      
| Web-2      | Backup, load-balanced  | 10.0.0.6    | Linux           |
|            | web server             |             |                 |
| Elk-Server | Store log data,        | 10.1.0.4    | Linux           |
|            | collects logs from     |             |                 |
|            | multiple machines,     |             |                 |
|            | provides visualiza-    |             |                 |  
|            | tion for data          |             |                 |





### Access Policies


The machines on the internal network are not exposed to the public Internet. 

Only the JumpBoxProvisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:  20.89.50.128

 - Whitelisted IP addresses:  52.140.199.168 (Load Balancer) and 20.213.91.70 (Elk Server)
  
Machines within the network can only be accessed by SSH with the username and IP address for the servers.

 - Which machine did you allow to access your ELK VM?

   JumpBoxProvisioner

 - What was its IP address?

   20.89.50.128


A summary of the access policies in place can be found in the table below.


| Name       | Publicly Accessible | Allowed IP Addresses         |
|------------|---------------------|------------------------------|
| Jump Box   | Yes                 | 10.0.0.5, 10.0.0.6, 10.1.0.4 |
| Elk-Server | No                  | 10.0.0.4, 10.0.0.5, 10.0.0.6 |
| Web-1      | No                  | 10.0.0.4, 10.0.0.6, 10.1.0.4 |
| Web-2      | No                  | 10.0.0.4, 10.0.0.5, 10.1.0.4 |                  




### Elk Configuration


Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because services running can be limited, system installation and update can be streamlined, and processed become more replicable.

 - What is the main advantage of automating configuration with Ansible?

The main advantage to automating configuration with ansible is that there are not special coding skills needed which makes it simple to set up and to use playbooks.   

The playbook implements the following tasks:

 - Identify target machine, ELK, and configure it to use more memory.
 - Install the Docker.io, Python3-pip and Pip packages
 - Download the Docker container “elk” version “761”   
 - Use ansible module docker-container with port mappings: 5601:5601, 9200:9200 and 5044:5044 to start containers
 - Enable the Docker server on boot


#### Explanation for the steps of the ELK installation playbook.

ELK is an acronym for the tools: Elasticsearch, Logstash and Kibana.  The Elasticsearch provides search and analytics in real-time for several different data types.  The Logstash collects that data from the multiple sources and sends it to Elasticsearch for analysis.  Kibana lastly takes that data collected and analyzed and provides shape for it also as a means to navigate, to manage and to monitor the entire ELK Stack. 

From my jump-box command prompt, RedAdmin@JumpBoxProvisioner:
 - sudo apt update 
 - sudo apt install docker.io

Check the status of Elasticsearch by running and retrieve my docker container list
 - sudo systemctl status docker
 - sudo docker container list -a

My docker container name is elastic_rosalind.  The “docker container start” starts container at any point, and “docker container attach” connects to running container 
 - sudo docker container start elastic_rosalind
 - sudo docker container attach elastic_rosalind

Now I am at my ansible container command prompt (root).  To get the SSH key
 - cat ~/.ssh/id_rsa.pub
 - cd /etc/ansible/

Nano into my files, YAML files, such as hosts, ansible.cfg and pentest.yml.  After editing and reviewing these files, can ssh into ELK server using its private IP address 10.1.0.4 and ensure that the sebp/elk:761 container is running by running docker ps.
 - ssh RedAdmin@10.1.0.4
 - sudo docker ps



The following screenshot displays the result of running “docker ps” after successfully configuring the ELK instance.

 

https://drive.google.com/file/d/1XAtk8o2tSfFq9_a3btmWnUPkUlhiSZ1R/view?usp=sharing





### Target Machines & Beats


This ELK server is configured to monitor the following machines:

 - Web-1    10.0.0.5
 - Web-2    10.0.0.6

We have installed the following Beats on these machines:

 - Filebeat
 - Metricbeat


These Beats allow us to collect the following information from each machine:

 - Filebeat is a “lightweight shipper for forwarding and centralizing log data.” It will monitor any specified log files and/or locations.  It also collects log        events such as server logs, audit logs, etc. then forwards it to Elasticsearch.

 - Metricbeat is a “lightweight agent installed on target servers to periodically collect metric data from your target servers.  It is used to monitor the servers      by collecting metrics from the system and service running on the server.




### Using the Playbook


In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 
SSH into the control node and follow the steps below:

Copy the /etc/ansible/filebeat-config.yml file to /etc/filebeat/filebeat.yml


#### Update the filebeat file to include...

 - Installing and Lauch Filebeat/hosts: webservers
 - Download filebeat .deb file/command: curl -L -O: https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb
 - Install fielbeat .deb/command: dpkg -I filebeat-7.4.0-amd64.deb
 - Drop in filebeat.yml /copy/dest: /etc/filebeat/filebeat.yml
 - Enable and Configure System Module/command: filebeat modules enable system
 - Setup filebeat/command: filebeat setup
 - Start filebeat service/command: service filebeat start
 - Enable service filebeat on boot/systemd/name: filebeat


Run the playbook and navigate to http://20.213.91.70:5601/app/kibana to check that the installation worked as expected.

 

https://drive.google.com/file/d/1-CvzuipPYLYsqQ7M8UHi2axgqTwTiQhj/view?usp=sharing

https://drive.google.com/file/d/1s91M32YHSupj3YI8zmyngnLpmEQE4HDw/view?usp=sharing



https://drive.google.com/file/d/1D0TuWBhfKuvkO5fPNEDcSkP1g081R7Vx/view?usp=sharing


Answer the following questions to fill in the blanks:

 - Which file is the playbook? 

   Filebeat-playbook.yml
   Src: /etc/ansible/filebeat-config.yml

 - Where do you copy it?
 
   Dest:  /etc/filebeat/filebeat.yml

 - Which file do you update to make Ansible run the playbook on a specific machine? 

   The host file is where list the of specific machines with their private IP addresses were updated.
 
   Example: [elk]
	     10.1.0.4 ansible_python_interpreter=/usr/bin/python3

How do I specify which machine to install the ELK server on versus which to install Filebeat on?

Both were configured and activated in Ansible at /etc/ansible#.  The ELK server is connected to my JumpBox, Web-1 and Web-2.  It is consuming a large quantity of input.  It analyzes, searches and collects a variety of data input from multiple sources simultaneously.  The Filebeat is connected to Web-1 and Web-2. It will allow administrators gather this data input with minimum RAM and CPU.  Filebeat consumes much fewer resources than the ELK server.
                  

Which URL do you navigate to in order to check that the ELK server is running?

Curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat >> 
/etc/ansible/filebeat-config.yml


As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc.

 - Ssh RedAdmin@20.89.50.128
 - RedAdmin@JumpBoxProvisioner:~$ sudo docker ps
 - sudo docker container list -a
 - sudo docker container start elastic_rosalind
 - sudo docker attach elastic_rosalind

Inside Ansible container, root
 - cd /etc/ansible

Check VMs are connected
 - ansible all -m ping
 - ssh RedAdmin@20.213.91.70
 - RedAdmin@Elk-Server:~$sudo docker container list -a
 - http://20.213.91.70:5600/app/kibana
 - RedAdmin@Elk-Server:~$ exit

Inside Ansible container, root
 - nano filebeat-congi.yml
 - curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat >> 
   /etc/ansible/filebeat-config.yml
 - ls
 - nano filebeat-playbook.yml

To excute the playbook
 - ansible-playbook filebeat-playbook.yml 

To run metricbeat, follow the same steps used to run filebeat

Inside Ansible container, root
 - nano metricbeat-congi.yml
 - curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Metricbeat >> 
   /etc/ansible/metricbeat-config.yml
 - ls
 - nano metricbeat-playbook.yml


