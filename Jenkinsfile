pipeline {
  agent any
  environment {
    dockerhub_account="iiimikusiii"
    app_name="pavel/netsko"
    dockerhub_pass="Removed"
  }

  stages {
    stage('Build and Dockerize'){
      steps {
        sh "docker build -t ${dockerhub_account}/$app_name:$BUILD_NUMBER ."
      }
    }

      stage('Push image to dockerhub'){
      steps {
              sh 'docker login -u ${dockerhub_account} -p ${dockerhub_pass} https://index.docker.io/v1/'
              sh "docker push ${dockerhub_account}/$app_name:$BUILD_NUMBER"    
        }

      }
    stage('Deploy'){
      steps {
          sh '''
             docker-machine create --driver amazonec2 \
             --amazonec2-access-key Removed \
             --amazonec2-secret-key Removed \
             --amazonec2-region eu-west-1 \
             --amazonec2-zone a \
             --amazonec2-instance-type "t2.micro" \
             --amazonec2-open-port 80 \
             ${app_name}-${BUILD_NUMBER}
             docker-machine scp -r -d ${WORKSPACE}/nginx/ ${app_name}-${BUILD_NUMBER}:/tmp/nginx
             eval $(docker-machine env ${app_name}-${BUILD_NUMBER})
             docker run -d -p 80:80 \
             --volume /tmp/nginx/conf/nginx.conf:/etc/nginx/conf/nginx.conf \
             --volume /tmp/nginx/html/index.html:/etc/nginx/html/index.html \
             ${dockerhub_account}/${app_name}:${BUILD_NUMBER}
          '''
          }
      }
  
  }
}
