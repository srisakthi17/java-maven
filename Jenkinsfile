pipeline{
    agent{
        label 'Master'
    }

    tools {
        maven 'Maven_3.9.5'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/srisakthi17/java-maven.git']])
            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

        stage('Sonar Scan'){
            steps{
                withSonarQubeEnv("Sonarqube") {
                    sh "${tool("Sonar_7.1")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://ec2-3-110-92-50.ap-south-1.compute.amazonaws.com:9000/ \
                    -Dsonar.login=sqp_f3552cf8fa654f4adb61393e7d08240136e104e4 \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=java-maven"
                }
            }
        }

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
