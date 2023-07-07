pipeline{
    agent any
    tools{
        maven 'Maven'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/kotianrakshith/CapstoneProject2']])
                sh 'mvn clean install'
            }
        }
        stage('Build Docker Image'){
            steps{
                script{
                    sh 'docker build -t kotianrakshith/insuranceapp .'
                }
            }
        }
        stage('Push Docker Image to Dockerhub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpassword')]) {
                    sh 'docker login -u kotianrakshith -p ${dockerhubpassword}'

                    sh 'docker push kotianrakshith/insuranceapp'
                    }
                }
            }
        }
        stage('Execute Ansible Playbook'){
            steps{
                    sh 'ansible-playbook ansible-playbook.yml'
            }
        }
        
    }
}
