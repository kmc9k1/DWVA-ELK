## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![aznet](https://github.com/kmc9k1/DWVA-ELK/blob/main/Diagrams/AzueVNfinal.png "Network Diagram")

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.
```
---
# install_elk.yml
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
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

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

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

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
The load balancer ensures that work to process incoming traffic will be shared by both vulnerable web servers.
Access controls will ensure that only authorized users — namely, ourselves — will be able to connect in the first place.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems and system metrics.

The configuration details of each machine may be found below.

| Name     | Function   | IP Address | Operating System |
|----------|------------|------------|------------------|
| Jump Box | Gateway    | 10.0.0.4   | Linux            |
| DWVA 1   | Webserver  | 10.0.0.7   | Linux            |
| DWVA 2   | Webserver  | 10.0.0.8   | Linux            |
| Elk      | Monitoring | 10.1.0.1   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the 'Jump Box' machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
71.238.127.205

Machines within the network can only be accessed by each other.
The DVWA 1 and DVWA 2 VMs send traffic to the ELK server.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Available | Allowed IP Addresses |
|----------|--------------------|----------------------|
| Jump Box | yes                | 71.238.127.205       |
| DWVA 1   | no                 | 10.0.0.1-254         |
| DWVA 2   | no                 | 10.0.0.1-254         |
| ELK      | no                 | 10.0.0.1-254         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because of ease of use and scalablity.
```
The playbook implements the following tasks:
-Installs docker.io
-Installs python3-pip
-Downloads and launches a docker ELK container
```
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![](https://github.com/kmc9k1/DWVA-ELK/blob/main/Diagrams/docker_ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
DWVA 1 - 10.0.0.7
DWVA 2 - 10.0.0.8

We have installed the following Beats on these machines:
```
Filebeat
Metricbeat
```
### These Beats allow us to collect the following information from each machine:
```
Filebeat: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
Metricbeat: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed sudo escalations, and CPU/RAM statistics
```
