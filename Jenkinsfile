pipeline {
    agent any

    environment {
        AMI_NAME = '' // Initialize AMI_NAME as an empty string
        COMMIT_ID = '' // Define an environmental variable to store the commit ID
    }

    stages {
        stage('packer init') {
            when {
                expression { return !params.SKIP_STAGES }
            }
            steps {
                dir('packer') {
                    sh 'whoami'
                    sh 'pwd'
                    sh 'packer init main.pkr.hcl'
                }
            }
        }
        stage('packer build') {
            when {
                expression { return !params.SKIP_STAGES }
            }
            steps {
                dir('packer'){
                    script {
                        def commitID = env.GIT_COMMIT
                        env.COMMIT_ID = commitID  // Set the commit ID as an environmental variable
                        echo "Commit ID: ${commitID}"

                        def packerOutput = sh(script: "packer build -machine-readable -var 'commit_id=${commitID}' main.pkr.hcl", returnStdout: true).trim()
                        
                        // Write the Packer output to a log file
                        writeFile file: 'packer_output.log', text: packerOutput
                        
                        // Extract the AMI name from the Packer output using regular expressions
                        def amiNameOutput = packerOutput =~ /demo_ami\.([^\n]+)/
                        if (amiNameOutput) {
                            AMI_NAME = "demo_ami.${amiNameOutput[0][1]}"
                            env.AMI_NAME_FIRST = "${AMI_NAME}${commitID}"
                            // Print the desired output format for the AMI name
                            echo "${AMI_NAME}${commitID}"
                            env.AMI_NAME = "${AMI_NAME}${commitID}"

                        } else {
                            error("Failed to retrieve the AMI name from Packer output.")
                        }
                    }
                }
            }
        }
    }
    post {
        success {
            script {
                echo "Triggering the next pipeline with AMI_NAME: ${AMI_NAME}"
                build job: 'aws-terraform-infra', parameters: [string(name: 'AMI_NAME', value: AMI_NAME)]
            }
        }
        failure {
            script {
                echo "Pipeline execution failed."
            }
        }
    }
}
