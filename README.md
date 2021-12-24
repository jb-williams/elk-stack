## Automated ELK Stack Deployment
### By Jb Williams
December 19, 2021

The files in this repository were used to configure the network depicted below.

![Net_Diagram](/Images/net_diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the yaml files may be used to install only certain pieces of it, such as Filebeat.

  * [Ansible Config](ansible.cfg):
    * Requres the proper remote user.
  * [Entire Deployment Setup](main.yml):
    * Requires private IP(s) added to [Hosts](hosts) file
  * [ELK Setup](roles/install-elk/tasks/main.yml):
    * Requires private IP(s) added to [Hosts](hosts) file
  * [Filebeat Setup](roles/install-filebeat/tasks/main.yml)
    * Requires private IP(s) added to [Hosts](hosts) file
    * Requires private IP(s) of ELK in the [Filebeat Configuration](roles/install-filebeat/files/filebeat-config.yml) file
  * [Metricbeat Setup](roles/install-metricbeat/tasks/main.yml)
    * Requires private IP(s) added to [Hosts](hosts) file
    * Requires private IP(s) of ELK in the [Metricbeat Configuration](roles/install-metricbeat/files/metricbeat-config.yml) file

This document contains the following details:
* Description of the Topology
* Access Policies
* ELK Configuration
  * Beats in Use
  * Machines Being Monitored
* How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load*balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly accessible, in addition to restricting traffic to the network.
* Load Balancers delegate and route traffic based on server capabilities and connection. This allows for more stability, uptime, and availability incase a server crashes or becomes inaccessible. Example Issue would be DDoS.
* The advantage of a jump box is that it allows the maintainer(s) a secure private backdoor to manage the network and machines

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the internal network and system logs.
* Filebeat captures log events or other admin specified events and transfers the data to the ELK server.
* Metricbeat collects metrics of activity and transfers the data to the ELK server.

The configuration details of each machine may be found below.

| Name       | Function | IP Address             | Operating System |
|------------|----------|------------------------|------------------|
| Jump Box   | Gateway  | 10.0.0.4/Static_Pub_IP | Linux            |
| Web-1      | Web App  | 10.0.0.7/104.43.220.6  | Linux            |
| Web-2      | Web App  | 10.0.0.6/104.43.220.6  | Linux            |
| Elk-Server | IDS      | 10.1.0.4/Static_Pub_IP | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
* The only machine that excepts connections from the internet is my the jump box from the Admin's Priviate IP.
* Only the Admin's public host IP through SSH is whitelisted.

Machines within the network can only be accessed by the Ansible Docker container on the Jump Box.
* The machine that connects to the ELK server within the network was the Ansible box on the Jump-box-provisioner. It connected from IP 10.0.0.4:22 to 10.1.0.4:22.
* To monitor from the Kibanna App, the Admin's public host IP was allowed to connect to port 5601 at the ELK VM's public IP address.

A summary of the access policies in place can be found in the table below.

| Name       | Publicly Accessible | Allowed IP Addresses                                        |
|------------|---------------------|-------------------------------------------------------------|
| Jump Box   | Yes                 | Admin_Host_Pub_IP:22                                        |
| Web-1      | No                  | Admin_Host_Pub_IP:80, 10.0.0.4:22                           |
| Web-2      | No                  | Admin_Host_Pub_IP:80, 10.0.0.4:22                           |
| Elk-Server | No                  | Admin_Host_Pub_IP:5601, 10.0.0.4:22                         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
* The main advantage of automating configuration is the ability to standardize configurations for any host accross the entire network.

The playbook implements the following tasks:
* Installs docker.io
* Installs python3-pip
* Installs docker (the docker module)
* Implements several ways of increasing and utilizing virtual memory
* Installs sebp/elk:761 ( the elk-stack) and map the proper ports
* Makes sure docker service is running

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Elk Docker Running](/Images/elk-install.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
* 10.0.0.6
* 10.0.0.7

We have installed the following Beats on these machines:
* Filebeat:
    * Installed on both Web-1 and Web-2
* Metricbeat 
    * Installed on both Web-1 and Web-2

These Beats allow us to collect the following information from each machine:
* [Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-overview.html):
    "Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to [Elasticsearch](https://www.elastic.co/products/elasticsearch) or [Logstash](https://www.elastic.co/products/logstash) for indexing."
Example Sudo Log from System Logs:
![Example_Sudo_Log](/Images/example_sudo_log.png)

* [Metricbeat](https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-overview.html):
    "Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash."
Example Metricbeat Dash:
![Example_Metric_Dash](/Images/example_metric_dash.png)

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
* Copy or clone the elk-stack repository, and copy all of its contents into the /etc/asnible directory.
* Make sure to update the ansible.cfg with the correct remote_user, and the hosts file with the proper Internal IP's of both Web VM's and the ELK Server VM into the proper categories.
* Run the playbook, and navigate to http://<elk_vm_public_ip>:5601/app/kibana to check that the installation worked as expected.

* The playbooks for each role is located in that role's respective "tasks/" directory, called main.yml.
* Updating the Internal IP of the Webservers and the ELK Server to the proper category of the "hosts" file designates which VM is hosting which application(s).
* Updating the parent main.yml, allows for easy desired role designation for certain hosts
* After running the playbook, navigating to http://<elk_vm_public_ip>:5601/app/kibana will allow the Admin to start configuring the dashboard and desired data.

| Action                                        | Command                                                               |
|-----------------------------------------------|-----------------------------------------------------------------------|
| (if using host)make sure ansible is installed | which ansible or apt install ansible                                  |
| (if using docker)pull docker image            | sudo docker pull <desired_ansible_image>
| (if docker)find docker image id               | sudo docker images                                                    |
| (if docker)run docker with terminal           | sudo docker run -it --name ansible-provisioner <Image_ID> bash        |
| clone repository                              | git clone https://github.com/jb-williams/elk-stack.git                |
| copy files to ansible directory               | cp -r elk-stack/* /etc/ansible/                                       |
| run entire deployment                         | ansible-playbook /etc/ansible/main.yml                                |
| run elk server                                | ansible-playbook /etc/ansible/roles/install-elk/tasks/main.yml        |
| run dvwa servers                              | ansible-playbook /etc/ansible/roles/install-dvwa/tasks/main.yml       |
| install filebeat on webservers                | ansible-playbook /etc/ansible/roles/install-filebeat/tasks/main.yml   |
| install metricbeat on webservers              | ansible-playbook /etc/ansible/roles/install-metricbeat/tasks/main.yml |

