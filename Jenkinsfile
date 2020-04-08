pipeline {
//    {dockerfile true} 
  agent any
  tools {
    maven 'maven3';
}
  stages {

    stage('Maven Build'){
        steps{
                sh label:'Maven Build of jar file', script:'''mvn test'''
        }
    }

    stage('Clover Analysis')
    {
        steps{
           sh "mvn clean clover:setup test clover:aggregate clover:clover"
           step([
                $class: 'CloverPublisher',
                cloverReportDir: 'target/site',
                cloverReportFileName: 'clover.xml',
                healthyTarget: [methodCoverage: 70, conditionalCoverage: 80, statementCoverage: 80], // optional, default is: method=70, conditional=80, statement=80
                unhealthyTarget: [methodCoverage: 50, conditionalCoverage: 50, statementCoverage: 50], // optional, default is none
                failingTarget: [methodCoverage: 0, conditionalCoverage: 0, statementCoverage: 0]     // optional, default is none
            ])
        }
    }
    
    stage('Checkstyle Analysis')
    {
        steps{
           sh "mvn clean clover:setup test clover:aggregate clover:clover"
           def mvnHome = tool 'maven3'

            sh "${mvnHome}/bin/mvn -batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs spotbugs:spotbugs"

            def checkstyle = scanForIssues tool: [$class: 'CheckStyle'], pattern: '**/target/checkstyle-result.xml'
            publishIssues issues:[checkstyle]

            def pmd = scanForIssues tool: [$class: 'Pmd'], pattern: '**/target/pmd.xml'
            publishIssues issues:[pmd]

            def cpd = scanForIssues tool: [$class: 'Cpd'], pattern: '**/target/cpd.xml'
            publishIssues issues:[cpd]

            def findbugs = scanForIssues tool: [$class: 'FindBugs'], pattern: '**/target/findbugsXml.xml'
            publishIssues issues:[findbugs]

            def spotbugs = scanForIssues tool: [$class: 'SpotBugs'], pattern: '**/target/spotbugsXml.xml'
            publishIssues issues:[spotbugs]
        }
    }

  }
}

