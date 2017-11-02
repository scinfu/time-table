	node('master') {

    stage('Checkout') {

        // Checkout files.
        checkout([
            $class: 'GitSCM',
            branches: [[name: 'master']],
            doGenerateSubmoduleConfigurations: false,
            extensions: [], submoduleCfg: [],
            userRemoteConfigs: [[
                name: 'github',
                url: 'https://github.com/scinfu/time-table.git'
            ]]
        ])
    }
    
    stage('Fabric') {
        def commitMessage = commitMessage()
        echo "commitMessage= $commitMessage";
    }
    
    stage('Tests') {
        // Build and Test
        sh 'xcodebuild test -scheme TimeTable -destination "platform=iOS Simulator,name=iPhone 7,OS=11.0" -enableCodeCoverage YES'
    }
/*
    stage ('Notify') {
        // Send slack notification
        slackSend channel: '#my-team', message: 'Time Table - Successfully', teamDomain: 'my-team', token: 'my-token'
    }*/
}

def commitMessage() {
    sh(returnStdout: true, script: 'git log -1 --pretty=%B').trim()
}

