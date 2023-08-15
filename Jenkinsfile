podTemplate(containers: [
    containerTemplate(
    name: 'docker',
    image: 'docker:dind',
    command: 'sleep',
    ttyEnabled: true,
    privileged: true,
    args: '30d',
    envVars: [
      containerEnvVar(key: 'IMAGE_NAME', value: 'course_catalog' ),
      containerEnvVar(key: 'NEXUS_REPOSITORY', value: '192.168.88.20:8082'),
      containerEnvVar(key: 'HTTP_PROTOCOL', value: 'http://'),
      containerEnvVar(key: 'REGISTRY_URL', value: 'http://192.168.88.20:8082')
      ]
),
    containerTemplate(
    name: 'openjdk', 
    image: 'openjdk:11', 
    command: 'sleep', args: '99d')],
   volumes: [
   hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
   ){
node(POD_LABEL) {

    environment {
      IMAGE_NAME="course_catalog"
      IMAGE_TAG="0.${BUILD_ID}"
      CONTAINER_IMAGE="${IMAGE_NAME}:${IMAGE_TAG}"
      HTTP_PROTOCOL="http://"
      NEXUS_REPOSITORY="192.168.88.20:8082"
      DOCKER_REGISTRY="${HTTP_PROTOCOL}${NEXUS_REPOSITORY}"
    }

    container('docker'){

    stage('Clone do repositorio') {
        git 'http://192.168.88.10:3000/odilon/simplePythonFlask.git'
   
    }
    stage('Dockerbuild') {
        sh 'echo $IMAGE_TAG'
        sh 'echo $IMAGE_NAME'
        sh 'echo $BUILD_NUMBER'
        sh 'echo $BUILD_ID'
        sh 'docker build -t "$IMAGE_NAME:$BUILD_ID" .'
        sh 'docker tag "$IMAGE_NAME:$BUILD_ID" "$NEXUS_REPOSITORY/$IMAGE_NAME:$BUILD_ID"'
    }
   stage('Unit Testing'){
	  sh 'docker run --rm -tdi --name unit "$NEXUS_REPOSITORY/$IMAGE_NAME:$BUILD_ID"'
    sh 'sleep 5'
    sh 'docker exec -t unit nosetests --with-xunit --with-coverage --cover-package=project test_users.py'
	  sh 'docker cp unit:/courseCatalog/nosetests.xml .'
	  sh 'docker stop unit'
   }
  stage('Gather test'){
  junit 'nosetests.xml'
  }
}

    container('openjdk'){
    stage('SonarQube Analysis'){
    script{
        def sonarScannerPath = tool 'SonarScanner'
        withSonarQubeEnv('SonarQube'){
        sh "${sonarScannerPath}/bin/sonar-scanner \
        -Dsonar.projectKey=courseCatalog -Dsonar.sources=."
    }
    }
    }
    }
  container('docker'){
  stage('Nexus - Saving Artifact'){
	 script{
	  docker.withRegistry('http://192.168.88.20:8082', '1d187952-2e25-43ef-ad56-3b074de189d0'){
	  sh 'docker push "$NEXUS_REPOSITORY/$IMAGE_NAME:$BUILD_ID"'
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
      sh   "docker image rm unit"
    }
  }
}