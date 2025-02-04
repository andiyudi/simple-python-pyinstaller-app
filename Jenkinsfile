node {
    stage('Build') {
        checkout scm
        docker.image('python:3.9').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        checkout scm
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        junit 'test-reports/results.xml'
    }
    stage('Manual Approval'){
        checkout scm
        input 'Lanjutkan ke tahap Deploy?'
        echo 'Deploying...'
    }
    stage('Deploy') {
            checkout scm
            docker.image('python:3.9').inside('-u root'){
                sh 'pip install pyinstaller'
                sh 'pyinstaller --onefile sources/add2vals.py'
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh 'scp -o StrictHostKeyChecking=no dist/add2vals ubuntu@54.169.178.177:/home/ubuntu/andiyudi/'
                }
            }
        echo 'Sleep for 1 min'        
        sh 'sleep 60'
    }
}