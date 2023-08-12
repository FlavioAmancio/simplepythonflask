pipeline {

  options {
    timestamps()
    timeout(time: 5, unit: 'MINUTES')
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
        sh 'docker build -t simplepythonflask -f simplePythonFlask/Dockerfile simplePythonFlask'

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