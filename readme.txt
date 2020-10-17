url to the dynamic web project (hw part-2)
http://ec2-52-23-203-211.compute-1.amazonaws.com:8080/MyWebProject/

Steps performed for the entire workflow for part 2:
	1. Install java, tomcat, eclipse. Set java_home to java download directory.
	2. Configure tomcat in eclipse and start
	3. Create a dynamic web project in ide. For the target runtime, select tomcat, check deployment descriptor checkbox and finish. Under webcontent directory in the project directory, create index.html file. 
	4. Create dir for css in project directory and create css stylesheet in that directory.
	5. Put css stylesheet path in index file.
	6. Right click on project --> run as --> run on server, select tomcat and finish. It will open the web form in the ide.
	7. Now open browser and give http://localhost:8080/MyWebProject/. It will open the form in browser.
	8. Right click on project --> export --> WAR file and provide destination location. It will create the project WAR file at destination location choosen.
	8. Now create linux based ec2 instance, ssh into it, install java, set java_path, install tomcat
	9. In tomcat config/tomcat-users.xml file set manager-gui,manager-status,admin-gui roles for tomcat user.
	10. Now start tomcat using startup.sh script under tomcat bin.
	11. Open tomcat in browser using ec2 endpoint on port 8080. Click manage app link and login using tomcat user and password set earlier. In "WAR file to deploy" section, upload the WAR file created earlier on local.
	12. Once WAR file is uploaded on tomcat, it will create the project directory under tomcat webapps folder and will explode the WAR file there.
	13. Now we can access the survey form by providing the ec2 endpoint with port and project name - "http://ec2-52-23-203-211.compute-1.amazonaws.com:8080/MyWebProject/".
