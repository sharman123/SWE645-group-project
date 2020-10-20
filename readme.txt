home page:
http://nshar.gmu.s3-website-us-east-1.amazonaws.com

Link to URL for forms deployed on kubernetes:
http://35.221.3.242/MyWebProject/

Instructions to recreate HW2:
create account with github and checkin the project code
create build.xml file for the project with target as "war" to build with ANT
create account with dockerhub and create docker file in the local project and check in the code to github
create account with googlecloud and create an Ubuntu based VM instance
ssh into the vm and install docker
using docker create container for rancher using below command:
sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/rancher
go to googlecloud console and create service account with owner privs, create and download the private key
go to rancher in browser using vm external IP, create new cluster, provide private key created in last step, select availability zone, set number of nodes as "1". it will create the cluster with one node
in browser click on "Kubeconfig File" button, copy the content, create ~/.kube/config on instance and paste the content copied earlier.
install openjdk and Jenkins to the VM using below commands:
sudo apt install openjdk-8-jre
   1  sudo apt-get install default-jre
   2  sudo apt-get install jenkins
   3  sudo service jenkins stop
   4  sudo service jenkins start 
add jenkins user to docker group-project using below cmd:
	sudo usermod -a -G docker jenkins
restart jenkins
	sudo /etc/init.d/jenkins restart
add the jenkins port(8080) with tcp to the inbound security group for VM so we can access it from another pc over http. to do this:
	go to "VPC Network" >> Firewall >> Create Firewall Rule, provide tcp for protocol, target tag same as vm instance tag and add Jenkins port and save
	
	Now open Jenkins in browser from local m/c by providing external ip of vm instance and port 8080
in Jenkins install plugins for ANT, Docker Build and Publish, SSH
go to Manage Jenkins >> Manage Credentials and save Docker credentials.
on VM create a private key and add it to Jenkins using private key option
go to Jenkins confirguration and set up remote host using cloud instance ip and putty key (generated from pem created in previous step)

in Jenkins create new Freestyle project,
	Check Github project and provide github project url
	in Source Code Management select "Git" and provide Repository url
	in Build Triggers, select Poll SCM and enter H * * * * (it will poll git for any changes periodically)
	add build step with "Invoke ANT" with target as WAR, build file as build.xml
	add another build step with "Docker Build and Publish" and 
		enter repository name (nsharm/swe645-group-project), Tag: latest
		registry credentials as docker cred saved earlier in Jenkins
	save the project and trigger a build. this will do a ANT build, create WAR file, create docker image and publish it to dockerhub.
 	
deployment the docker image to kubernetes cluster: ssh to vm instance and run followign commands:
	kubectl create deployment swe645 --image=docker.io/nsharm/swe645-group-project:latest
	kubectl expose deployment swe645 --type=LoadBalancer --port=80 --target-port=8080
	kubectl autoscale deployment swe645 --cpu-percent=50 --min=3 --max=5
it will create three replicas of our group project on cluster node

now the project can be accessed at following url:
http://35.221.3.242/MyWebProject/

now go back to Jenkins project > Configure, add build step with "Execute shell script on remote host using ssh" option.
	enter teh SSH site as VM username@external IP
	enter folling command in Command text box:
		kubectl rollout restart deployment.apps/swe645

To verify the CICD works, make some change to the form index file and checkin the code. it will trigger automatic Jenkins build. once build is complete. go to the forms url and verify the changes are reflected.
http://35.221.3.242/MyWebProject/