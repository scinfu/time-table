pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'xcodebuild test -scheme TimeTable -destination "platform=iOS Simulator,name=iPhone 7,OS=11.0" -enableCodeCoverage YES'
            }
        }
    }
}
