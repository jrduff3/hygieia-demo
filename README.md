# Hygieia POC

Hygieia℠ is a single, configurable, easy to use dashboard to visualize near real-time status of the entire delivery pipeline. For more information on Hygieia please refer to the [GitHub repository](https://github.com/capitalone/Hygieia) and [CapitalOne DEVEXCHANGE](https://developer.capitalone.com/opensource-projects/hygieia/).

Hygieia was being researched as a possible solution for issue life cycle management and monitoring. This would provide a mechanism to provide traceability from a specific project JIRA issue creation and link this to specific Git repository code commit. It would also provide a way to monitor and link all related DevOps components like static code analysis (SonarQube) and build tools (i.e. Jenkins). The following documentation walks through configuring and some of the issues setting up and running the Hygieia dashboard.

All the components for Hygieia were installed via docker. Each component to run Hygieia must be first built with maven then a docker build can be executed to build the image and add the application properties. The only component that didn't require a maven build is the Mongo database.
If you follow the guides you should be able to get this up and running. I did run into a few issues along the way but most of them were configuration issues and were identified from debugging the logs. One issue I did encounter was with configuring the SonarQube Hygieia collector. It would not hook in and after debugging it was due to the source code for the collector was not compatible with the version of SonarQube I was using. There was an update that resolved this issue a couple weeks later and I was able to hook this in.

Due to the complexity of the configuration I deployed all the applications and collectors with docker. Then developed a docker-compose.yml file and push this to github https://github.com/jrduff3/hygieia-demo.git along with the Mongo db data volume. All the corresponding docker images were committed and pushed up to dockerhub. To reproduce the POC please use the following steps:
Note: you will need Docker and docker-compose installed along with Git.

```bash
$ git clone https://github.com/jrduff3/hygieia-demo.git
Cloning into 'hygieia-demo'...
remote: Counting objects: 124, done.
remote: Compressing objects: 100% (45/45), done.
remote: Total 124 (delta 78), reused 122 (delta 76), pack-reused 0
Receiving objects: 100% (124/124), 173.91 MiB | 9.45 MiB/s, done.
Resolving deltas: 100% (78/78), done.
Checking connectivity... done.
Checking out files: 100% (105/105), done.
```

Now go into the hygieia-demo directory and run "docker-compose up -d" This part will take a few minute because it will need to download all the docker images and start up the applications:

```bash
$ cd hygieia-demo/
[hygieia-demo]$ docker-compose up -d 
WARNING: The Docker Engine you're using is running in swarm mode.
 
Compose does not use swarm mode to deploy services to multiple nodes in a swarm. All containers will be scheduled on the current node.
 
To deploy your application across the swarm, use `docker stack deploy`.
 
Pulling mongodb (cprimedevops/mongodb:latest)...
latest: Pulling from cprimedevops/mongodb
e45e882ed798: Pull complete
b03f96593290: Pull complete
90df9ef9b571: Pull complete
a647e09745f6: Pull complete
b394c03fdf0b: Pull complete
081d72a1938b: Pull complete
7584b1f09d77: Pull complete
9504d8d990d3: Pull complete
ef2c764578cc: Pull complete
2369c7a1c288: Pull complete
bb0dcc8bccd7: Pull complete
9fb5815867a0: Pull complete
Digest: sha256:db5fbcbef1b6237b285e2a39d0248c3cb72fac41a7365e32c661a8f4d2ea1434
Status: Downloaded newer image for cprimedevops/mongodb:latest
Pulling hygieia-api (cprimedevops/hygieia-api:latest)...
latest: Pulling from cprimedevops/hygieia-api
846bf2170f96: Already exists
a3ed95caeb02: Already exists
6b04cfa6ca9c: Already exists
5143f090214c: Already exists
8d825e2e6fa4: Already exists
2dee158e1c76: Already exists
.
.
.
latest: Pulling from cprimedevops/hygieia-github-collector
846bf2170f96: Already exists
a3ed95caeb02: Already exists
6b04cfa6ca9c: Already exists
5143f090214c: Already exists
8d825e2e6fa4: Already exists
2dee158e1c76: Already exists
a3a6e58cccbd: Already exists
58980f0906b9: Already exists
01376126a64b: Already exists
f0c9808d4ebf: Pull complete
0ccd7292a671: Pull complete
c52970548358: Pull complete
Digest: sha256:dbb27eeddcf9dd0f467c8da6c152f353bccc36acf06bb44ad5e89e73865f5f2d
Status: Downloaded newer image for cprimedevops/hygieia-github-collector:latest
Creating mongodb ...
Creating jira ...
Creating sonarqube ...
Creating mongodb
Creating jira
Creating sonarqube ... done
Creating hygieia-api ...
Creating jenkins ...
Creating hygieia-api
Creating hygieia-api ... done
Creating hygieia-jira ...
Creating jenkins ... done
Creating hygieia-ui ...
Creating hygieia-jenkins-build ...
Creating hygieia-sonar-build ...
Creating hygieia-ui
Creating hygieia-github
Creating hygieia-jira
Creating hygieia-jenkins-build
Creating hygieia-sonar-build ... done
```

When you execute a docker ps you should see all the containers running:

```bash
$ docker ps
CONTAINER ID        IMAGE                                              COMMAND                  CREATED              STATUS              PORTS                                                                                   NAMES
9a2bf7854527        cprimedevops/hygieia-sonar-codequality-collector   "/bin/sh -c './sonar-"   About a minute ago   Up 58 seconds                                                                                               hygieia-sonar-build
b9aa56caafa6        cprimedevops/hygieia-jenkins-build-collector       "/bin/sh -c './jenkin"   About a minute ago   Up About a minute                                                                                           hygieia-jenkins-build
f6de73781046        cprimedevops/hygieia-jira-feature-collector        "/bin/sh -c './jira-p"   About a minute ago   Up About a minute                                                                                           hygieia-jira
f614808d8c8f        cprimedevops/hygieia-ui:latest                     "/bin/sh -c 'conf-bui"   About a minute ago   Up About a minute   443/tcp, 0.0.0.0:8088->80/tcp                                                           hygieia-ui
f4f617aeeb27        cprimedevops/hygieia-github-collector              "/bin/sh -c './github"   About a minute ago   Up About a minute                                                                                           hygieia-github
77417d80280e        cprimedevops/jenkins:hygieiaDemo                   "/bin/sh -c 'java -ja"   About a minute ago   Up About a minute   0.0.0.0:8090->8080/tcp                                                                  jenkins
9109cd417bf1        cprimedevops/hygieia-api:latest                    "/bin/sh -c './proper"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp                                                                  hygieia-api
23315281dc19        cprimedevops/mongodb:latest                        "docker-entrypoint.sh"   About a minute ago   Up About a minute   0.0.0.0:27017->27017/tcp                                                                mongodb
929b5a0ad0c7        cprimedevops/jira:hygieiaDemo                      "/bin/sh -c '/opt/app"   About a minute ago   Up About a minute   0.0.0.0:8081->8081/tcp                                                                  jira
e140e18f34cb        cprimedevops/sonarqube                             "./bin/run.sh"           About a minute ago   Up About a minute   0.0.0.0:9000->9000/tcp, 0.0.0.0:9092->9092/tcp                                          sonarqube
```

Now you can go to a browser and enter the following to access the Hygieia dashboard:
http://${docker-host-ip-address}:8088 
This will bring you to the Hygieia dashboard:

![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/dashboard1.png)

You can login hto the dashboard by selecting the Login button:![alt text](https://github.com/jrduff3/hygieia-demo/blob/master/loginbutton.png) on te upper right pane of the screen:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/login.png)

The username is "user" and password is "password"
Then select the Consumer Application dashboard under all dashboards:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/calogin.png)

![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/cadashboard.png)

Now you can log into Jenkins: http://${docker-host-ip-address}:8090
username: admin
password: password

![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jenkinslogin.png)

Select the ConsumerApplication project:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jenkinsdashboard.png)

Then click build now on the left pane:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jenkinsproject.png)

You should see build 7 pop up:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jenkinsprogress.png)

Select the status bar and this will bring you to the console output:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jenkinsconsoleoutput.png)

Once the build is complete it will take up to 5 mins to sync with the Hygieia Dashboard. You should now see a Build 7:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/hygieia-jenkins.png)

You can also log into JIRA at http://${docker-host-ip-address}:8081:
user: admin
password: password
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jiralogin.png)

Go to the ConsumerApplicationKanban project:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jiraproject.png)

Create a new issue using the create button on the top navigation pane. 
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jiracreate1.png)

Enter a Summary and description for the new issue and select create:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/jiracreate2.png

You should now see the issue appear in the Hygieia dashboard under the Feature widgets "issues in progress":
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/hygieia-jira.png)

The code repo widget by default for this POC is hooked into https://github.com/jrduff3/consumerapplication.git. You can configure another repo by selecting the configuration icon(![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/settings.png)) and update the configuration for another GitHub repo:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/gitconfig.png)

The POC also has SonarQube server and the widget configured. To access SonarQube Server go to http://${docker-host-ip-address}:9000 and login:
username: admin
password: admin
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/sonarqubelogin.png)

Select projects and show all projects:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/sq1.png)

The ConsumerApplication project is the analysis pushed from the Jenkins build:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/sq2.png)

This information shows up in the Hygieia's Quality widget, currently this project does not have any code coverage but the lines analyzed should match up:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/hygieia-sq.png)

### Mongo Database:
The Mongo database holds the collections from all the collectors. If the git commits contain the JIRA issue key in the commit message it would be possible to link this data and create a new dashboard widget to show the traceability. Here is an example of the document for the different types of collections to show what data is available:
Document from Source Code Collection:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/robomongo.png)

Document from Build Collection:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/robomongo2.png)

Document from Feature Collection:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/robomongo3.png)
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/robomongo4.png)

Document from Code Quality Collection:
![alt text](https://github.com/jrduff3/hygieia-demo/images/blob/master/robomongo5.png)

