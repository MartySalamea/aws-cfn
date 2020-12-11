##Download the files and run:

aws cloudformation validate-template --template-body file://vpc.yaml


##To create stack:

aws cloudformation create-stack --stack-name vpc-cli --template-body file://vpc.yaml

##To update stack:

aws cloudformation update-stack --stack-name vpc-cli --template-body file://vpc.yaml

##Run command with parameters

aws cloudformation create-stack --stack-name apacheEC2 --template-body file://ec2.yaml --parameters  ParameterKey=SubnetType,ParameterValue=PublicSubnet

scp  -i "virginia-kp.pem" virginia-kp.pem  ec2-user@54.157.27.155:/home/ec2-user/







AWSTemplateFormatVersion: 2010-09-09
Description: "create simple ec2"
# Transform: AlarmMacro
Parameters:
  SubnetID:
    Type: AWS::EC2::Subnet::Id
    Description: The list of Subnet in your Virtual Private Cloud (VPC)
  
  KpName:
    Type: AWS::EC2::KeyPair::KeyName
    Description: The list of Key Pair in your Virtual Private Cloud (VPC)
  EC2size:
    Description: Choose EC2 size
    Type: String
    Default: t2.micro # or t2.nano or t2.small
    AllowedValues:
      - t2.micro
      - t2.nano 
      - t2.small
Mappings:
  SubnetMap: # MapName
    us-east-1: # TopLevelKey
      PublicSubnet:  subnet-0e4ef558ed6c52491 # SecondLKey 
      PrivateSubnet: subnet-0c5f40152342d153a # SecondLKey
      LinuxImg: ami-0323c3dd2da7fb37d  # SecondLKey
    us-east-2:
      PublicSubnet:  subnet-034b7a57df40c0056 
      PrivateSubnet: subnet-002740a289412ddc0  
      LinuxImg:  ami-0f7919c33c90f5b58 
      
  
Resources:
  # myEC2Instance01:
  #   Type: AWS::EC2::Instance
  #   Properties:
  #     SubnetId: !FindInMap [ SubnetMap, !Ref "AWS::Region", !Ref SubnetType ]  
  #     ImageId:  !FindInMap [ SubnetMap, !Ref "AWS::Region", LinuxImg ] 
  #     InstanceType: !Ref EC2size
  #     UserData: !Base64 |
  #       #!/bin/bash
  #       yum update -y
  #       yum install httpd -y
  #       service httpd start
  #       chkconfig httpd on
  #       cd /var/www/html
  #       echo "<html><h1>This is Server01</h1></html>" > index.html
  myEC2Instance02:
    Type: AWS::EC2::Instance
    Properties:
      KeyName: !Ref KpName
      SubnetId: !Ref SubnetID 
      ImageId:  !FindInMap [ SubnetMap, !Ref "AWS::Region", LinuxImg ] 
      InstanceType: !Ref EC2size
      Tags:
          - Key: Name
            Value: Server01
      UserData: !Base64 |
        #!/bin/bash
        yum update -y
        yum install httpd -y
        service httpd start
        chkconfig httpd on
        cd /var/www/html
        echo "<html><h1>This is Public Server</h1></html>" > index.html
        sudo amazon-linux-extras install epel -y
        sudo yum install stress -y
        # sudo stress --cpu 4 --io 3 --vm 2 --vm-bytes 256M --timeout 120s