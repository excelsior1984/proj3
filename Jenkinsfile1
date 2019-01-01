pipeline {

agent any
environment {
      JAVA_HOME="${tool 'jdk8'}"
      PATH="${env.JAVA_HOME}/bin:${env.PATH}"
      CC = 'clang'
}


tools {
      jdk 'jdk8'
      maven 'maven3'
}

stages {

        stage('Install') {
           steps {
               sh 'java -version'
               echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
               echo "JAVA_HOME=  ${env.JAVA_HOME}  PATH=  ${env.PATH}"
               sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
           }
           post {
                always {
                       junit '**/target/*-reports/TEST-*.xml'
		       step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
                }
           }
       }
       stage('Sonar') {
          steps {
                sh "mvn sonar:sonar -Dsonar.host.url=${env.SONARQUBE_HOST}"
            }
        }

        stage ('deploy'){
            steps{
                configFileProvider([configFile(fileId: '49a1c083-fc08-4126-99e8-c25b786e72c6', variable: 'SETTINGS')]) {
                    sh "mvn -s $SETTINGS deploy -DskipTests -Dartifactory_url=${env.ARTIFACTORY_URL}"
                }
            }
        }
}
}