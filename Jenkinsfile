//This is aws credential method 

pipeline {
    agent any

    parameters {
        choice(
            name: 'AWS_REGION',
            choices: [
                'us-east-1',
                'us-west-1',
                'us-west-2',
                'eu-central-1',
                'eu-west-1',
                'ap-south-1'
            ],
            description: 'Select the AWS region'
        )
    }

    environment {
        AWS_REGION = "${params.AWS_REGION}"
    }

    stages {
        stage('List AWS Resources') {
            steps {
                script {
                    echo "Listing resources in the region: ${AWS_REGION}"

                    withCredentials([
                        usernamePassword(
                            credentialsId: 'aws-credentials',
                            usernameVariable: 'AWS_ACCESS_KEY_ID',
                            passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                        )
                    ]) {
                        echo "Fetching EC2 Instances..."
                        sh """
                            aws ec2 describe-instances --region ${AWS_REGION} \\
                            --query "Reservations[*].Instances[*].{InstanceId:InstanceId, State:State.Name, InstanceType:InstanceType, PublicIpAddress:PublicIpAddress}" \\
                            --output table
                        """

                        echo "Fetching EBS Volumes..."
                        sh """
                            aws ec2 describe-volumes --region ${AWS_REGION} \\
                            --query "Volumes[*].{VolumeId:VolumeId, Size:Size, State:State}" \\
                            --output table
                        """

                        echo "Fetching Security Groups..."
                        sh """
                            aws ec2 describe-security-groups --region ${AWS_REGION} \\
                            --query "SecurityGroups[*].{GroupName:GroupName, GroupId:GroupId, Description:Description, VpcId:VpcId}" \\
                            --output table
                        """

                        echo "Fetching Elastic IPs..."
                        sh """
                            aws ec2 describe-addresses --region ${AWS_REGION} \\
                            --query "Addresses[*].{PublicIp:PublicIp, InstanceId:InstanceId}" \\
                            --output table
                        """
                    }
                }
            }
        }
    }
}
