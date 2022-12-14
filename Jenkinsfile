pipeline {
  agent any
  stages {
    stage('Status') {
      agent { docker { image 'liquibase/liquibase:4.17' } }
      steps {
        sh 'liquibase status --url="jdbc:postgresql://database-1.cpuc6bgspxr2.eu-central-1.rds.amazonaws.com:5432/postgres" --changeLogFile=changelog_version.xml --username=postgres --password=fellaini'
        sh 'liquibase status --url="jdbc:postgresql://database-1.cpuc6bgspxr2.eu-central-1.rds.amazonaws.com:5432/postgres" --changeLogFile=changelog_version.xml --username=postgres --password=fellaini'
      }
    }
    stage('initmvn') {
      steps {
        sh 'mvn --version'
      }
    }
    stage('test') {
       steps {
        sh 'mvn clean package -Dmaven.test.skip=true'
        sh 'mvn liquibase:update'  
        sh 'mvn liquibase:status -PTEST'
        sh 'nohup mvn spring-boot:run'
      }
    }
    stage('QA') {
      steps {
        sh 'mvn clean package'
        sh 'mvn liquibase:update --url=$env:QA_URL --changeLogFile=$env:changeLogFile --username=postgres --password=fellaini'
        sh 'mvn liquibase:status -PQA'
        sh 'mvn Spring-boot:run'
       }
      }
    stage('Update') {
      steps {
        sh 'liquibase update --url="$QA_URL" --changeLogFile=$changeLogFile --username=postgres --password=fellaini'
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}
