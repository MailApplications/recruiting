# Introduction
The aim of this test is to evaluate how the candidate handles common daily problems from our environments. It is encouraged to use any means possible to search for solutions.

Our team, Mail Applications, have been selected as owner of a new service called 'ThinkFreak', where users can post articles on a Wiki style page.

The service was made online since a few weeks for some selected users, and now it is time to make it available to all users.

Unfortunately, since this morning, the service is not working anymore. We would like to ask you to take a look and fix all the problems preventing the service to be available on a **production environment**.

# Architecture
This service consists of a PHP application, connected to a PostgreSQL DB, a monitoring system, a Load Balancer and a spare server:

* poma-web.thinkfreak.de → Server where the application is deployed
* poma-db.thinkfreak.de → Server where the DB is deployed
* poma-mon.thinkfreak.de → Server where the Monitoring system is deployed
* poma-lb.thinkfreak.de → Server where the Load Balancer is configured
* poma-backend.thinkfreak.de → spare server, which should be monitored, but isn't serving any customer services at the moment
 
![Architecture](/images/architecture.png)

# Instructions
* Using the internet (Google, Stackoverflow, etc) is **allowed**.
* Read the tasks carefully
* Assume that you are working on a **Live** system, impacting ***thousands of clients***
* Ask as many questions as you want
* If you feel stuck, ask for some tips
* You are being evaluated as HOW you tackle problems in real life. Solving all the problems is not a requirement of this test.
* Please share your desktop, where you search the internet
* You will use tmate to connect to the service hosts. This way, we can connect to the same terminal and follow your progress.
* DNS configuration looks like that: *.thinkfreak.de and thinkfreak.de are pointing to the external IP addresses of **poma-lb**
* There is a network firewall installed protecting the external interfaces of the hosts and only allowing TCP ports (80, 443, 22)
* Files / credentials / certificates needed to be able to fulfill tasks can be found on all machines, in the folder `/root/pomatest` 

# Hosts and their Credentials

Provided during the interview

# Tasks
In order to make this service available to more users, some improvements are required:

* Yesterday we could reach the web-service on http://poma-web.thinkfreak.de ... today we can't reach it anymore
  * Please fix it
* A new host was created to act as a Load Balancer: **poma-lb**
  * Install Nginx/Traefix to act as a reverse proxy with SSL offloading
  * Proxy web app from poma-web on the internal interface to www.thinkfreak.de / thinkfreak.de with TLS. Make sure users are automatically forwarded from http to https.
  * Bonus:
    * Check your domains with SSLlabs. How can you improve the SSLlabs results?
    * Proxy Grafana from **poma-mon** on the internal interface with TLS to grafana.thinkfreak.de
* There was a decision to use a separated monitoring system for this service. A host (poma-mon) was created with Icinga2, prometheus and Grafana.
  * Please create a new dashboard to monitor CPU / LOAD / MEMORY on the 4 hosts (poma-web, poma-mon, poma-db and poma-lb)
  * Bonus:
    * create the dashboard above using dynamic variables
  * Please create a Nagios compliant script for checking iostat for 30 seconds and place it on **poma-web** in the folder: ```/usr/share/icinga2-check-plugins``` 
    No argument-parsing needed, you can use **"sda"** as device and hardcoded thresholds for warning and critical state.
    Check is already added to the Icinga2 instance at: http://poma-mon.thinkfreak.de/icingaweb2


But, before you start, there was an incident this morning. A System Administrator executed an Ansible playbook by mistake on the 4 hosts, and we cannot guarantee the state of the service.

Please check if all components are up and running as expected.


**So it is expected to have a running service, following the proposed architecture, with a working dashboard in Grafana.***

# Tips
* Check your connection to all 4 hosts
* Check if the services are working properly
* Make yourself familiar with the systems / landscape
  * Which OS is running?
  * Which network interfaces / addresses / hostnames are used?
  * Which systems are reachable by which connections/interfaces.
  * How are systems secured?
* To debug problems on the web-app on **poma-web** enable the debug option ```'debug' => filter_var(env('DEBUG', true), FILTER_VALIDATE_BOOLEAN``` 
  in file ```/var/www/blog/config/app_local.php``` and check the startpage of the web app.
