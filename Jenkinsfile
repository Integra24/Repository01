pipeline {
    agent any

    stages {
        stage('Get code') {
            steps {
                // Obtener código del repositorio
                git 'https://github.com/Integra24/Repository01.git'
            }
        }
        stage('Static') {
              steps {
		 bat '''
   			set PYTHONPATH=%WORKSPACE%
                        PATH= C:\\UNIR_DEVOPS\\SOFT\\Scripts
	              	flake8 --format=pylint --exit-zero app >flake8.out
                 '''
                recordIssues tools: [flake8(name: 'Flake8', pattern: 'flake8.out')], qualityGates: [[threshold: 8, type: 'TOTAL', unstable: true], [threshold: 10, type: 'TOTAL', unstable: false]]
            }
        }
        stage('Unit') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    bat '''
                        set PYTHONPATH=%WORKSPACE%
                        PATH= C:\\UNIR_DEVOPS\\SOFT\\Scripts
                        pytest --junitxml=result-unir.xml test\\unit
                    '''
                }    
            }
           
        }

        stage('Rest') {
            steps {
                bat '''
                    set FLASK_APP=app\\api.py
                    start C:\\UNIR_DEVOPS\\SOFT\\Scripts\\flask run
                    start java -jar C:\\UNIR_DEVOPS\\SOFT\\wiremock-standalone-3.5.4.jar --port 9090 --root-dir test\\wiremock
                    set PYTHONPATH=%WORKSPACE%
                    C:\\UNIR_DEVOPS\\SOFT\\Scripts\\pytest --junitxml=result-rest.xml test\\rest
                '''
            }
        }
        stage('Result') {
            steps {
                    junit 'result*.xml'
                    echo 'FINISH!!!'
            }
        }  
        
    }
}
