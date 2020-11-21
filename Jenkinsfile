pipeline {
   agent any
   environment {
       app = 'shopping_app'
       service = 'service2'
       registry = 'deepanmurugan/python2'
       registryCredential = 'dockerhub'
       dockerImage = ''
   }
   stages {
       stage('Build') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER" 
                }
            } 
        }
       stage('Test') {
           steps {                
               sh 'echo "Testing the docker built image"'
           }
       }
       stage('Publish') {
           steps{
               script {
                     docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                   }
               }
           }
       }
       stage('Pull Playbook Repo') {
        steps {
          checkout poll: false, scm: [$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CloneOption', noTags: false, reference: '', shallow: false, timeout: 10]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github_repo', url: 'https://github.com/deepanmurugan/Ansible_Playbook.git']]]
          }
       }
       stage ('Deploy') {
           steps {
               script{
                   def image_id = registry + ":$BUILD_NUMBER"
                   sh "ansible-playbook deploy_k8s.yml --extra-vars \"image_id=${image_id}\" \"app_name=${app_name}\" \"service_name=${service}\""
               }
           }
       }
   }
}
