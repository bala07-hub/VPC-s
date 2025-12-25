SELECT VPC FROM AWS ACCOUNT
Create New VPC
Select VPC and more
Give a name to it
Leave all other options as default
Select create VPC button. (VPC will be created)
Now, Select EC2 service from AWS
Launch instance
Give name and remaining settings accordingly
However, makesure edit Network settings  Info > VPC - required > Select the VPC which we created (default VPC shouldn't be selected here)
Availability Zone.(Select public just for this demo. Always private is recommended)
Auto-assign public IP > This option also should be enabled
Now, check other options and click launch instance
From terminal ssh to the ec2 instance
ssh -i <.pem file location> ubuntu@<public address of the ec2-instance>
run the commands from app-setup.sh
Set inbound rules of ec2 instance
Set inbound rules of Network ALC's 
Your project will be open and running as per the rules set
