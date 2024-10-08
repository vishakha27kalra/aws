pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1' // Change to your region
        INSTANCE_ID = 'i-01d24802a35a8d0af' // Change to your instance ID
    }

    stages {
        stage('aws-authentication') {
            steps {
                script {
                    withCredentials([[
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'official-aws',
                        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY',
                    ]]) {
                        sh 'aws --version'
                        def volId = sh(script: "aws ec2 describe-volumes --filters Name=attachment.instance-id,Values=${env.INSTANCE_ID} --query 'Volumes[*].VolumeId' --output text", returnStdout: true).trim()
                        echo "Volume ID: ${volId}"

                        // def volId = sh(script: "aws ec2 describe-instances --instance-ids ${env.INSTANCE_ID} --query 'Reservations[*].Instances[*].BlockDeviceMappings[*].Ebs.VolumeId' --output text", returnStdout: true).trim()
                        // echo "Volume ID: ${volId}"
                        //def snapshotOutput = sh(script: "aws ec2 create-snapshot --region us-east-1 --volume-id ${volId} --output json", returnStdout: true).trim()
                    
                        def snapshotOutput = sh(script: """
                            aws ec2 create-snapshot --region us-east-1 --volume-id ${volId} --output text --query 'SnapshotId'
                        """, returnStdout: true).trim()
                        
                        echo "Snapshot Output: ${snapshotOutput}"    
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
        failure {
            echo 'Build failed. Please check the logs for details.'
        }
        success {
            echo 'Build succeeded!'
        }
    }
}
