pipeline {
    agent any

    tools { 
        maven 'jenkinsmaven' 
        jdk 'JAVA' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[url: 'git@github.com:ToboCodes/portfolioProject.git']]
                ])
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: "**/target/*.jar", fingerprint: true
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
                junit '**/target/surefire-reports/TEST-*.xml'
            }
        }

        stage('Sonar Scanner') {
            steps {
                script {
                    def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
                        sh "${sonarqubeScannerHome}/bin/sonar-scanner -e -Dsonar.host.url=http://SonarQube:9000 -Dsonar.login=${sonarLogin} -Dsonar.projectName=mv-maven -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=GS -Dsonar.sources=src/main/java/com/kibernumacademy/miapp -Dsonar.tests=src/test/java/com/kibernumacademy/miapp -Dsonar.language=java -Dsonar.java.binaries=."
                    }
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        
        // stage('Nexus Upload') {
        //     steps {
        //         nexusArtifactUploader(
        //             nexusVersion: 'nexus3',
        //             protocol: 'http',
        //             nexusUrl: 'nexus:8081',
        //             groupId: 'cl.awakelab.junitapp',
        //             version: '0.0.1-SNAPSHOT',
        //             repository: 'maven-snapshots',
        //             credentialsId: 'nex',
        //             artifacts: [
        //                 [artifactId: 'proyectoJunit',
        //                 classifier: '',
        //                 file: 'target/proyectoJunit-0.0.1.jar',
        //                 type: 'jar'],
        //                 [artifactId: 'proyectoJunit',
        //                 classifier: '',
        //                 file: 'pom.xml',
        //                 type: 'pom']
        //             ]
        //         )
        //     }
        // }
    }
}