pipeline {

    agent any

    options {
        timestamps()
    }

    parameters {

        choice(
            name: 'ENV',
            choices: ['DEV', 'UAT', 'PROD'],
            description: 'Select Target Environment'
        )

        string(
            name: 'USERS',
            defaultValue: '100',
            description: 'Number of Virtual Users'
        )

        string(
            name: 'RAMPUP',
            defaultValue: '60',
            description: 'Ramp-up Time (seconds)'
        )

        string(
            name: 'DURATION',
            defaultValue: '300',
            description: 'Test Duration (seconds)'
        )

        string(
            name: 'TEST_PLAN',
            defaultValue: 'Enterprise_API_Performance_Framework_new.jmx',
            description: 'JMeter Test Plan'
        )
    }

    environment {

        PROJECT_HOME = "${WORKSPACE}\\jmeter"
        JMETER_HOME  = "${WORKSPACE}\\jmeter\\apache-jmeter-5.6.3"

    }

    stages {

        stage('Checkout Information') {

            steps {

                echo "========================================="
                echo "Enterprise JMeter Performance Pipeline"
                echo "========================================="

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

                echo "Workspace Prepared Successfully."

            }
        }

        stage('Run JMeter Test') {

            steps {

                bat """
                cd /d "${env.PROJECT_HOME}"

                "${env.JMETER_HOME}\\bin\\jmeter.bat" -n ^
                -t "${env.PROJECT_HOME}\\${params.TEST_PLAN}" ^
                -Jusers=${params.USERS} ^
                -Jrampup=${params.RAMPUP} ^
                -Jduration=${params.DURATION} ^
                -l "${env.PROJECT_HOME}\\results\\results.jtl" ^
                -e ^
                -o "${env.PROJECT_HOME}\\reports"
                """

                echo "JMeter Test Execution Completed Successfully."

            }
        }

        stage('Archive Results') {

            steps {

                archiveArtifacts(
                    artifacts: 'jmeter/results/results.jtl',
                    fingerprint: true,
                    allowEmptyArchive: false
                )

                echo "JTL File Archived Successfully."

            }
        }

        stage('Publish HTML Report') {

            steps {

                publishHTML(target: [

                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'jmeter/reports',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report'

                ])

                echo "JMeter HTML Report Published Successfully."

            }
        }

    }

    post {

        always {

            echo "========================================="
            echo "Performance Test Completed"
            echo "========================================="

        }

        success {

            echo "Pipeline Executed Successfully."

        }

        failure {

            echo "Pipeline Failed."

        }
    }
}