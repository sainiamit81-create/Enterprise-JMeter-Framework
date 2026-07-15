pipeline {

    agent any

   environment {
    PROJECT_HOME = "C:\\Users\\DELL\\Documents\\Enterprise-JMeter-Framework\\jmeter"
    JMETER_HOME  = "${PROJECT_HOME}\\apache-jmeter-5.6.3"
}

    stages {

        stage('Run JMeter Test') {
    steps {
        bat """
        cd /d "%PROJECT_HOME%"

        if exist reports rmdir /s /q reports
        if exist results\\results.jtl del /f /q results\\results.jtl

        mkdir reports

        "%JMETER_HOME%\\bin\\jmeter.bat" -n ^
        -t "%PROJECT_HOME%\\Enterprise_API_Performance_Framework_new.jmx" ^
        -l "%PROJECT_HOME%\\results\\results.jtl" ^
        -e ^
        -o "%PROJECT_HOME%\\reports"
        """
    }
}

        stage('Archive Results') {
            steps {
                echo 'Archiving Results...'
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
