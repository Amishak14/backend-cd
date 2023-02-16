pipeline {
    agent any
   // parameters { string(name: 'GITHUB_EMAIL', defaultValue: '', description: 'Github Email') 
     //            string(name: 'GITHUB_USERNAME', defaultValue: '', description: 'Github username') }
    stages {
        stage("Checkout") {
            steps {

               checkout scm
        }


    }

        stage("update git"){
        steps{
            withCredentials([usernamePassword(credentialsId: 'my-git', passwordVariable: 'pass', usernameVariable: 'user')]) {
                  script {
                        env.encodedPass=URLEncoder.encode(pass, "UTF-8")
                    }

 

        sh 'git config user.email "${GITHUB_EMAIL}"'
        sh 'git config user.name  "${GITHUB_USERNAME}"'
        sh "cat manifest.yaml"
        echo "${DOCKERTAG}"
        sh "sed -i 's+image-registry.openshift-image-registry.svc:5000/amisha-jenkins/expense-tracker-backend:.*+image-registry.openshift-image-registry.svc:5000/amisha-jenkins/expense-tracker-backend:${DOCKERTAG}+g' ./my-folder/manifest.yaml"
        sh "cat manifest.yaml"
        sh "git add ."
        sh "git commit -m 'done by jenkins backend-cd-pipeline' "
       // sh "git pull"
        sh 'git push https://$user:$encodedPass@github.com/$user/backend-cd.git HEAD:branch'
            }
        }
        }
        
        
         stage("deploy the application") {
        steps {
            script {
                openshift.withCluster() {
                    openshift.withProject("$PROJECT_NAME") {
                        echo "Using project: ${openshift.project()}"
                         sh 'oc project "$PROJECT_NAME"'
                         sh 'oc apply -f manifest.yaml'
                    }
                 }
            }
        } 
    }  
        }
}
