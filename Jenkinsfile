pipeline{
    agent{
        label 'Master'
    }

    tools {
        maven 'maven_3.9.0'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/DevOps-SVC04/java-maven-war-app.git']])
            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

        // stage('Sonar Scan'){
        //     steps{
        //         withSonarQubeEnv("SonarQube") {
        //             sh "${tool("Sonar_5.0.1")}/bin/sonar-scanner \
        //             -Dsonar.host.url=http://13.235.69.189:9000/ \
        //             -Dsonar.login=sqp_0c07fd0d029a2928a7f9a656ce9486e029a7affa \
        //             -Dsonar.java.binaries=target \
        //             -Dsonar.projectKey=java-maven-war-app"
        //         }
        //     }
        // }

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            agent{
                label 'Ansible'
            }
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
