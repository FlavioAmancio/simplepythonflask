pipeline {
    stages{
        // Estagio de build da aplicação
        stage ('Build')
            steps{
                sh "docker build -t simple-python-flask ."
            }
    }
}