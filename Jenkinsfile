pipeline {
    agent any

    parameters {
        string(name: 'REPO_URL', defaultValue: 'https://github.com/wokie1/cicd-demo-app.git')
        string(name: 'GIT_BRANCH', defaultValue: 'master')
        booleanParam(name: 'RUN_TESTS', defaultValue: true)
    }

    stages {
        stage('Checkout') {
            steps {
                deleteDir()
                git url: params.REPO_URL, branch: params.GIT_BRANCH
                echo "Ветка: ${params.GIT_BRANCH}"
                sh 'git rev-parse HEAD'
            }
        }

        stage('Validate') {
            steps {
                sh '''
                    test -f README.md &&
                    test -f requirements.txt &&
                    test -d src &&
                    test -d tests
                '''
            }
        }

        stage('Test') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                sh 'python3 -m unittest discover -s tests -v'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    set -e
                    echo "BUILD_NUMBER=$BUILD_NUMBER" > build-info.txt
                    git rev-parse HEAD >> build-info.txt
                    git archive -o app.tar HEAD
                    tar -rf app.tar build-info.txt
                    gzip app.tar
                '''
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'app.tar.gz'
            }
        }
    }

    post {
        success { echo 'Сборка успешна' }
        failure { echo 'Сборка провалена' }
    }
}
