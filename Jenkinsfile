node('Node') {

stage('Checkout/Build/Test') {

// Checkout files.
checkout([
$class: 'GitSCM',
branches: [[name: 'master']],
doGenerateSubmoduleConfigurations: false,
extensions: [], submoduleCfg: [],
userRemoteConfigs: [[
name: 'github',
url: 'https://github.com/oludemilade/shakeMeSilly.git'
]]
])

// Build and Test
sh 'xcodebuild -scheme "shakeMeSilly" -configuration "Debug" build test -destination "platform=iOS Simulator,name=iPhone 8,OS=12.1" -enableCodeCoverage YES | ocunit2junit'

// Publish test restults.
step([$class: 'JUnitResultArchiver', allowEmptyResults: true, testResults: 'build/reports/junit.xml'])
}

stage('Analytics') {

parallel Coverage: {
// Generate Code Coverage report
sh '/usr/local/bin/slather coverage --jenkins --html --scheme TimeTable shakeMeSilly.xcodeproj/'

// Publish coverage results
publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'html', reportFiles: 'index.html', reportName: 'Coverage Report'])


}, Checkstyle: {

// Generate Checkstyle report
sh '/usr/local/bin/swiftlint lint --reporter checkstyle > checkstyle.xml || true'

// Publish checkstyle result
step([$class: 'CheckStylePublisher', canComputeNew: false, defaultEncoding: '', healthy: '', pattern: 'checkstyle.xml', unHealthy: ''])
}, failFast: true|false
}

stage ('Notify') {
// Send slack notification
slackSend channel: '#my-team', message: 'Time Table - Successfully', teamDomain: 'my-team', token: 'my-token'
}
}
