pipeline {

    agent any
	
    parameters {
	
        string (name: 'BRANCH_NAME', 
                defaultValue: 'master',
                description: 'Branch')
				
        choice (
            choices: 'DEBUG\nRELEASE\nTEST',
            description: '',
            name: 'BUILD_TYPE')
			
		text (name: 'MULTI',
			  defaultValue: 'DESKTOP-6NA9BGE\nGA990FXAUD3T\nDO755E8400\nWIN-SRHDRL5EICG',
			  description: 'Multiline string')
			
		string (name: 'Filename',
				defaultValue: 'Test.txt',
				description: 'Name of the text file file to create.')
				
		string (name: 'FileContent',
				defaultValue: 'This is a test.',
				description: 'Content text for the file.')
			
    }
	
    stages {
        
        stage('process') {
            
            when {
                
                allOf {
                    expression {params.BRANCH_NAME == "master"}
                    expression {params.BUILD_TYPE == 'DEBUG'}
                }
                
            }
            
            steps {
                
                echo "Kicking off debug build\n"
                
                script {
					
					String[] arr = params.MULTI.split("\\r?\\n");
				    
					for (ii = 0; ii < arr.size(); ii++) {
					
						//println "${arr[ii]}"
						
						if (arr[ii].trim() != '') {
							
							//powershell(script: "Write-Output ${arr[ii]}")
								
							withCredentials ([usernamePassword(credentialsId: 'PowerShellTest', passwordVariable: 'Password', usernameVariable: 'UserName')]) {
	
								withEnv(["Computer=${arr[ii]}"]) {
									
									def status = powershell(returnStatus: true, script: """
										. '.\\CreateFiles.ps1'
										CreateFiles
									""")
									
									if (status == 0) {
										
										// Success!
										println "Success"
										
									} else {
										
										// Failure!
										println "Failure"
										
									} 
									
								}
								
							}
							
						}
						
					}
					
                }
				
            }
			
        }
		
    }
	
}
