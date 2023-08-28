pipeline {
    agent any
triggers {
        pollSCM '* * * * *'
}
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
           	script{
                	echo "${env.WORKSPACE}"
					typestage = "Build the code for 48 V"
			   		env.stageLine = "=" * typestage.length()
                	echo "${'\n'}${env.stageLine}${env.stageLine}${'\n'}${typestage}${'\n'}${env.stageLine}${env.stageLine}${'\n'}"
					bat 'del polyspacebatchmode.m'
                    bat 'set Path = C:\\Program Files\\MATLAB\\R2023a\\bin'
                    bat 'echo addpath(\'%WORKSPACE%\\%BRANCH_NAME%\\NSI-48V-DC-DC-Build\'); >> polyspacebatchmode.m'
                    bat 'echo  build -b; >> polyspacebatchmode.m'
                    bat '"C:\\Program Files\\MATLAB\\R2023a\\bin\\matlab.exe" -batch polyspacebatchmode'    
				}
            }
post {
				always{
					dir("${WORKSPACE}"){
						script{
							if(!fileExists('console.log')){
								writeFile file: 'console.log.txt', text:currentBuild.rawBuild.log
							}
								
							def consoleOutput = readFile('console.log.txt')
                            
							if (consoleOutput.contains('JENKINS-ERROR_003_MODEL_COMPILATION')){
							    buildError="JENKINS-ERROR_003_MODEL_COMPILATION: Build aborted  due to model compilation errors."
							}
							if (consoleOutput.contains('JENKINS-ERROR_004_MODEL_BUILD')){
								buildError="JENKINS-ERROR_004_MODEL_BUILD: Build aborted  due to model code generation errors."
							}
							if (consoleOutput.contains('JENKINS-ERROR_005_INVALID_BATCH_INPUT')){
								buildError="JENKINS-ERROR_005_INVALID_BATCH_INPUT: Build aborted  due to build batch was invoqued with incorrect arguments."
							}
							if (consoleOutput.contains('JENKINS-ERROR_006_DCDC_SOFTWARE_COMPILATION')){
								buildError="JENKINS-ERROR_006_DCDC_SOFTWARE_COMPILATION: Build aborted  due to DCDC Converter software compilation errors."
							}
							if (consoleOutput.contains('JENKINS-ERROR_007_EHPE_SOFTWARE_COMPILATION')){
								buildError="JENKINS-ERROR_007_EHPE_SOFTWARE_COMPILATION: Build aborted  due to EHPE Heater software compilation errors."
							}
							if (consoleOutput.contains('JENKINS-ERROR_08_A2L_GENERATION')){
								buildError="JENKINS-ERROR_08_A2L_GENERATION: Build aborted  due to A2l generation error."
							}
							if (consoleOutput.contains('JENKINS-ERROR_09_CAL_GENERATION')){
								buildError="JENKINS-ERROR_09_CAL_GENERATION: Build aborted  due to Cal generation error."
							}
						   if (buildError==""){
                            	echo buildError
							    echo "Successful Build Stage!"
							}
							else{
								error (buildError)
							}
						}
					}
				}			
        } 
        }
        
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
