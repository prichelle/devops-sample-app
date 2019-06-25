//Name of credential object in Jenkins
creds = "apic-apidev"
commitId = "001"
appName = "mySampleApp".toLowerCase()
registryURL = "http://localhost:80";

namespace = "labs"
    parameters {
        string(defaultValue: "mysampleapp", description: 'name of the app', name: 'appNameParam')
        //choice(choices: ['master', 'acc'], description: 'What branch ?', name: 'branch')
    }
 
node {    

    try{
        //load server env.
        //load "$JENKINS_HOME/.envvars/apicenv"
        // intServer = env.apichost
        
        echo "Workspace: ${env.WORKSPACE}"
 
        sh 'ls -la'
        sh 'git log --abbrev-commit --pretty=oneline -1'

        commitId = sh (
          			script: 'git log --abbrev-commit --pretty=oneline -1 | awk  \'{print $1}\'',
          			returnStdout: true
        		).trim()
        echo "commit id: ${commitId} " 
        //Publish to integration server
        publish(creds, commitId, appName, namespace, registryURL)  

    } catch(exe)
    {
        echo "${exe}"
        error("[FAILURE] Failed to initialize")
    }
}    

def publish(String creds, String commitId, String myAppName, String namespace, String registryURL) {
        
        stage ('Build') {
                echo 'Running build automation'
                imageName = namespace + "/" + myAppName + ":" + commitId
                echo "building ${imageName}"
                docimg = docker.build(imageName)
        } 
        stage ('reg-load') {
            echo 'Loading built image into registry'
                
            script {
                docker.withRegistry(registryURL, 'docker-registry') {
                    docimg.push(commitId)
                }
            }
            echo 'images available in the catalog'
            withCredentials([usernameColonPassword(credentialsId: 'docker-registry', variable: 'USERPASS')]) {
                sh 'curl -X GET http://localhost:80/v2/_catalog -u $USERPASS'
            }
        }

}

def deployincluster(){
    
                //echo 'deploying using helm'
                //sh 'helm install --name=helloapp --namespace=labs ./helm'
                //sh 'helm list'

}