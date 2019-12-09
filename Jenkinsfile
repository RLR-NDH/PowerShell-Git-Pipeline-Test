pipeline {

    agent any
	
    parameters {
	
        string (name: 'BRANCH_NAME', 
                defaultValue: 'master',
                description: '')
        choice (
            choices: 'DEBUG\nRELEASE\nTEST',
            description: '',
            name: 'BUILD_TYPE')
			
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
									
									def status = powershell(returnStatus: true, script: '''
										
										# Ensure the build fails if there is a problem.
										# The build will fail if there are any errors on the remote machine too!
										
										$ErrorActionPreference = 'Stop'
										
										# Create a password variable using the stored password from the Jenkins Global Passwords
										
										$SecurePassword = $env:Password | ConvertTo-SecureString -AsPlainText -Force
										
										# Create a PSCredential Object using the a hardcorded username and and $SecurePassword variable
										
										$cred = New-Object System.Management.Automation.PSCredential -ArgumentList $env:UserName, $SecurePassword
										
										# Build an object to store variables to send remotely
										
										$objForRemote = @{}
										
										# Add build paramters to the object
										
										$objForRemote.FileName    = $env:FileName
										$objForRemote.FileContent = $env:FileContent
										
										# (Optional) I like to output the $objForRemote variable so I can confirm by looking at the Jenkins Console Output of the build I set my parameters correctly.
										
										Write-Output $objForRemote
										
										# Invoke a command on the remote machine, sending $objForRemote to the ArgumentList paramater.
										# It depends on the type of job you are executing on the remote machine as to if you want to use "-ErrorAction Stop" on your Invoke-Command.
										
										Invoke-Command -ComputerName $env:Computer -Credential $cred -ArgumentList $objForRemote -ScriptBlock {
										#Invoke-Command -ScriptBlock {
											
											# Get the arguments passed to the remote session into the same variable name for ease of use
											
											$objForRemote = $args[0]
											
											# Create Temp Directory
											
											if (-not(Test-Path -Path "C:\\temp")) {
												
												New-Item -Path "C:\\temp" -ItemType directory
												
											}
											
											# Using the environment variables exposed by the Jenkins job 
											
											Set-Content -Path "C:\\temp\\$($objForRemote.FileName).txt" -Value $objForRemote.FileContent
											
										} 
										
									''')
									
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
