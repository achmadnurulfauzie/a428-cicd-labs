pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        id: 'userInput',
                        message: 'Lanjutkan ke tahap Deploy?',
                        parameters: [
                            choice(name: 'choice', choices: 'Proceed\nAbort', description: 'Choose an action')
                        ]
                    )
                    
                    if (userInput == 'Proceed') {
                        echo 'Melanjutkan eksekusi pipeline ke tahap Deploy...'
                    } else {
                        error('Eksekusi pipeline dihentikan oleh pengguna.')
                    }
                }
            }
        }
        stage('Deploy') { 
            steps {
                script {
                    sh './jenkins/scripts/deliver.sh'
                    echo 'Menjeda eksekusi pada Deploy stage selama 1 menit...'
                    sleep(time: 1, unit: 'MINUTES') // Pause for 1 minute
                    echo 'Melanjutkan eksekusi pipeline setelah menjeda...'
                } 
                sh './jenkins/scripts/kill.sh' 
            }
        }
    }
}
