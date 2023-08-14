pipeline {

  options {
    timestamps()
    timeout(time: 5, unit: 'MINUTES')
  }

environment {
  IMAGE_NAME="course_catalog"
  IMAGE_TAG="0.${BUILD_ID}"
  CONTAINER_IMAGE="${IMAGE_NAME}:${IMAGE_TAG}"
  HTTP_PROTOCOL="http://"
  NEXUS_REPOSITORY="192.168.88.20:8082"
  DOCKER_REGISTRY="${HTTP_PROTOCOL}${NEXUS_REPOSITORY}"
}

  agent any
  stages {
    stage('Clone do repositorio') {
      steps {
        sh 'git clone http://192.168.88.10:3000/odilon/simplePythonFlask.git'
      }
    }
    stage('Dockerbuild') {
      steps {
        sh 'docker build -t "${CONTAINER_IMAGE}" -f simplePythonFlask/Dockerfile simplePythonFlask'
        sh 'docker tag "${CONTAINER_IMAGE}" "${NEXUS_REPOSITORY}/${CONTAINER_IMAGE}"'
      }
    }
   stage('Nexus - Saving Artifact'){
	steps{
	 script{
	  docker.withRegistry("${DOCKER_REGISTRY}", '1d187952-2e25-43ef-ad56-3b074de189d0'){
	  sh 'docker push "${NEXUS_REPOSITORY}/${CONTAINER_IMAGE}"
	}
      }
     }
   }
}
  post {
    always {
      echo "Pipeline finalizado."
      sh   'rm -rf simplePythonFlask'
    }
    success {
      echo "Pipeline finalizado com Sucesso"
    }
    failure {
      echo "Pipeline finalizado com Falha"
    }
  }
}
