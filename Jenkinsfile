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
                url: 'https://github.com/mmorejon/time-table.git'
            ]]
        ])
    }
    
    stage('Fabric') {
        //def commitHash = checkout(scm).GIT_COMMIT
        //def commitMessage = sh 'git log -1 --pretty=%B'
        //def commitMessage = sh 'git log -1 --pretty=%B'
        sh "git log -1 --pretty=%B > result";
        def output=readFile('result').trim()
        echo "output=$output";
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

def version() {
    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
    matcher ? matcher[0][1] : null
}

def commitSha1() {
    sh 'git rev-parse HEAD > commit'
    def commit = readFile('commit').trim()
    sh 'rm commit'
    commit.substring(0, 7)
}

def commitMessage() {
    def commitMessage = sh 'git log -1 --pretty=%B'
    return commitMessage
}
