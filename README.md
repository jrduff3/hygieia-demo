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
