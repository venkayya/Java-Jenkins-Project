pipeline {
  agent none
  stages {
     stage('Unit Tests') {
       agent {
         label 'apache'
       }
       steps {
         sh 'ant -f test.xml -v'
         junit 'reports/result.xml'
         }  
     }
     stage('build') {
       agent {
         label 'apache'
       }
       steps {
          sh 'ant -f build.xml -v'
       }
       post {
          success {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
          }  
       } 
     }
     stage('deploy') {
       agent {
         label 'apache'
       }
       steps {
         sh "if [! -d '/var/www/html/rectangles/all/$BRANCH_NAME']; then mkdir '/var/www/html/rectangles/all/BRANCH_NAME' fi"
           sh "cp dist/rectangle_$BUILD_NUMBER.jar /var/www/html/rectangles/all/$BRANCH_NAME/"
       }
     }
     stage('Running on Centos') {
       agent {
           label 'CentOS'
       }
       steps {
           sh "wget http://venky6371.mylabserver.com/rectangles/all/$BRANCH_NAME/rectangle_$BUILD_NUMBER.jar"
           sh "java -jar rectangle_$BUILD_NUMBER.jar 3 4"
       }    
     }
     stage('Test On Debain') {
       agent {
           docker 'openjdk:latest'
       }
       steps {
           sh "wget http://venky6371.mylabserver.com/rectangles/all/$BRANCH_NAME/rectangle_$BUILD_NUMBER.jar"
           sh "java -jar rectangle_$BUILD_NUMBER.jar 3 4"
       }
     }
     stage('Promote to Green') {
       agent {
           label 'apache'
       }
       when {
           branch 'master'
       }
       steps {
           sh "cp /var/www/html/rectangles/all/rectangle_$BUILD_NUMBER.jar /var/www/html/rectangles/green/rectangle_$BUILD_NUMBER.jar"    
      }
    }
    stage('Promote Development Branch to Master') {
       agent {
           label 'apache'
       }
       when {
           branch 'development'
       }
       steps {
           echo "Stashing Any Local Changes"
           sh 'git stash'
           echo "Checking Out Development Branch"
           sh 'git checkout development'
           echo "Checing Out Master Branch"
           sh 'git checkout master'
           echo "Merging Development Branch to Master Branch"
           sh 'git merge development'
           echo "Pushing to Origin Master"
           sh 'git push origin master'
       }
    }
  }
}
