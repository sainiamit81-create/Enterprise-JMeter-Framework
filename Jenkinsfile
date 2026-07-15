pipeline {

    agent any

    parameters {
        choice(
            name: 'ENV',
            choices: ['DEV', 'UAT', 'PROD'],
            description: 'Select Environment'
        )

        string(
            name: 'USERS',
            defaultValue: '100',
            description: 'Virtual Users'
        )

        string(
            name: 'RAMPUP',
            defaultValue: '60',
            description: 'Ramp-up Time (seconds)'
        )

        string(
            name: 'DURATION',
            defaultValue: '300',
            description: 'Duration (seconds)'
        )

        string(
            name: 'TEST_PLAN',
            defaultValue: 'Enterprise_API_Performance_Framework_new.jmx',
            description: 'JMeter Test Plan'
        )
    }

    environment {
        PROJECT_HOME = "C:\\Users\\DELL\\Documents\\Enterprise-JMeter-Framework\\jmeter"
        JMETER_HOME  = "C:\\Users\\DELL\\Documents\\Enterprise-JMeter-Framework\\jmeter\\apache-jmeter-5.6.3"
    }

    stages {

        stage('Checkout Information') {
            steps {
                echo "Environment : ${params.ENV}"
                echo "Users       : ${params.USERS}"
                echo "Ramp-up     : ${params.RAMPUP}"
                echo "Duration    : ${params.DURATION}"
                echo "Test Plan   : ${params.TEST_PLAN}"
            }
        }

        stage('Prepare Workspace') {
            steps {
                bat '''
                cd /d "%PROJECT_HOME%"

                if exist reports rmdir /s /q reports
                if exist results\\results.jtl del /f /q results\\results.jtl

                mkdir reports
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                cd /d "%PROJECT_HOME%"

                "%JMETER_HOME%\\bin\\jmeter.bat" -n ^
                -t "%PROJECT_HOME%\\%TEST_PLAN%" ^
                -Jusers=%USERS% ^
                -Jrampup=%RAMPUP% ^
                -Jduration=%DURATION% ^
                -l "%PROJECT_HOME%\\results\\results.jtl" ^
                -e ^
                -o "%PROJECT_HOME%\\reports"
                '''
            }
        }

        stage('Archive Results') {
            steps {
                archiveArtifacts artifacts: 'jmeter/results/*.jtl', fingerprint: true, allowEmptyArchive: true
                archiveArtifacts artifacts: 'jmeter/reports/**', fingerprint: true, allowEmptyArchive: true

                echo 'JMeter execution completed successfully.'
            }
        }
    }

    post {

        always {
            echo 'Performance Test Completed'
        }

        success {
            echo 'Pipeline Successful'
        }

        failure {
            echo 'Pipeline Failed'
        }
    }
}