pipeline {
  agent any

  triggers {
    pollSCM('* * * * *')
  }

  stages {
    stage("Compile") {
      steps {
        sh "./gradlew compileJava"
      }
    }

    stage("Unit test") {
      steps {
        sh "./gradlew test"
      }
    }

    stage("Code coverage") {
      steps {
        sh "./gradlew jacocoTestReport"
        publishHTML (target: [
               reportDir: 'build/reports/jacoco/test/html',
               reportFiles: 'index.html',
               reportName: "JaCoCo Report" ])
        sh "./gradlew jacocoTestCoverageVerification"
      }
    }

    stage("Static code analysis") {
      steps {
        sh "./gradlew checkstyleMain"
        publishHTML (target: [
               reportDir: 'build/reports/checkstyle/',
               reportFiles: 'main.html',
               reportName: "Checkstyle Report" ])
      }
    }

    stage("Static code analysis by sonar") {
      steps{
      withSonarQubeEnv("owasp_sonar"){
      sh "./gradlew sonarqube -Dsonar.host.url=http://172.20.10.2:9000/ -Dsonar.login=8a88f667d8d71bc776ea1399bd21d70b6818484e"
          }
        }
      }

      stage("Quality Gate") {
              steps {
                timeout(time: 1, unit: 'HOURS') {
                  waitForQualityGate abortPipeline: true
                }
              }
            }

    stage("Build") {
      steps {
        sh "./gradlew build"
      }
    }

    stage("Docker build") {
      steps {
        sh "docker build -t harshith1989/calculator:${BUILD_TIMESTAMP} ."
      }
    }

    stage("Docker login") {
      steps {
          sh "docker login --username harshith1989 --password T@t@1234"
      }
    }

    stage("Docker push") {
      steps {
        sh "docker push harshith1989/calculator:${BUILD_TIMESTAMP}"
      }
    }

  }
}
