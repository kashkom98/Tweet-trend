pipeline {
    agent {
        node {
            label "maven"
        }
    }

    stages {
        stage('Clone code') {
            steps {
                // echo 'Hello World'
                git branch: 'main', url: 'https://github.com/kashkom98/Tweet-trend.git'
            }
        }
    }
}