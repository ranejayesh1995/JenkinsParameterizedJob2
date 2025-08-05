//This is aws credential method 
//go to aws search for IAM
//on IAM dashboard under IAM resources click on users
//create user > add username
//next > select attached policies directly>
//checked the checkbox AdministratorAccess >next
//Create User> view user
//use the access key and password from aws to below steps
// access key and password should be add in jenkins> 
//credential >
//click on global > 
//Add Credentials >
//select username and password add username as access key and password as secret access key and id i have added as it is same Create 
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
                            credentialsId: 'aws-credentials', //this id should be matched or any id you can give but
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
