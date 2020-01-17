# simple-jenkins-pipeline
Code repository for a simple jenkins job (a CI pipeline) that builds, tests and packages a forked Java app.

## Configure Job
1. naviagte to the root Jenkins folder on your desktop and find the folder jobs. (Should look something like C/Jenkins/jobs/)
2. clone the git repository into the 'jobs' folder.
3. refresh your jenkins and you'll find a new job created.

## Groovy Script
The following is the groovy script used inside the pipeline to do the following:
+ clone the git repository of the Java app.
+ build, test and package the code into a .jar file.
+ archive the .jar file and publish HTML test reports to the Jenkins interface.
+ notify user if job fails.

```node {
    notify('Started')
    try{
        stage 'checkout'
        git 'https://github.com/Neveen-E/jenkins2-course-spring-petclinic.git'
          
        stage 'compile, test, package'
        bat 'mvn clean verify'
   
    } catch (err){
        notify("Error ${err}")
        currentBuild.result = 'FAILURE'
    }
    
     stage 'archival'
        publishHTML([allowMissing: true, 
        alwaysLinkToLastBuild: false, 
        keepAll: true, 
        reportDir: 'target/site/jacoco/', 
        reportFiles: 'index.html', 
        reportName: 'Code Coverage', 
        reportTitles: ''])
        step([$class: 'JUnitResultArchiver', testResults: 'target/surefire-reports/TEST-*.xml'])
        archiveArtifacts "target/*.?ar
}

def notify(status){
    emailext (
      to: "email@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}
```

Note: the above script is for compatible with windows.
