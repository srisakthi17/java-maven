pipeline{
    agent{
        label 'agent1'
    }

    tools {
        maven 'Maven_3.9.7'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/releases']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Viven/java-maven-war-app.git']])
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
       //             sh "${tool("Sonar_4.6.2")}/bin/sonar-scanner \
       //             -Dsonar.host.url=http://65.2.80.139:9000/ \
       //             -Dsonar.login=sqp_b2c12d602066827db01278fafdc99c9c377d9d36 \
       //             -Dsonar.java.binaries=target \
       //             -Dsonar.projectKey=java-maven-war-app"
       //        }
       //      }
       // }

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            agent{
                label 'agent1'
            }
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
