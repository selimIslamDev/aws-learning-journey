# Day 11 - IaC, CloudFormation & Terraform

## Date: 22 April 2026

---

## IaC কেন দরকার এবং Main কাজ কী?

**বাংলা:** IaC মানে Infrastructure as Code। সহজ কথায় — AWS এ manually click করে resource বানানোর বদলে code লিখে বানানো। যেমন তুমি manually EC2, VPC, S3 বানাও — IaC দিয়ে একটা file লিখলেই সব automatically বানানো হয়।

**English:** Infrastructure as Code (IaC) is the practice of managing and provisioning infrastructure through code instead of manual processes. Instead of clicking through the AWS Console, you write code that describes your desired infrastructure.

**কেন IaC দরকার — Real Problem:**

ধরো তোমার company তে ৫০টা EC2, ১০টা VPC, ২০টা S3 bucket বানাতে হবে। Manually করলে:
- ঘণ্টার পর ঘণ্টা লাগবে
- ভুল হতে পারে
- আরেকবার করতে হলে আবার same কাজ

IaC দিয়ে:
- একটা file লিখো
- Run করো — সব automatically হয়ে যাবে
- পরেরবার same file run করলে same infrastructure পাবে

**IaC এর Main ৪টা সুবিধা:**

| সুবিধা | মানে |
|--------|------|
| Automation | Manual কাজ নাই |
| Consistency | প্রতিবার same result |
| Version Control | Git এ track করা যায় |
| Reusability | একই code বারবার use |

---

## CloudFormation কেন দরকার?

**বাংলা:** CloudFormation হলো AWS এর নিজস্ব IaC tool। YAML বা JSON file লিখলে AWS automatically সব resource বানায়। যেমন একটা file এ EC2 + VPC + S3 define করলে — CloudFormation সব বানিয়ে দেবে।

**English:** AWS CloudFormation is AWS's native IaC service that allows you to define your AWS infrastructure in YAML or JSON templates. CloudFormation then provisions and manages those resources automatically.

**Use Case:**
- নতুন environment বানাতে (Dev, Staging, Production)
- Disaster recovery এ same infrastructure recreate করতে
- Team এ consistent infrastructure দিতে

---

## Terraform কেন দরকার?

**বাংলা:** Terraform হলো HashiCorp এর IaC tool। CloudFormation শুধু AWS এ কাজ করে — Terraform AWS, Azure, GCP সব জায়গায় কাজ করে। Multi-cloud environment এ Terraform best choice।

**English:** Terraform is an open-source IaC tool by HashiCorp that works with multiple cloud providers (AWS, Azure, GCP) and hundreds of services. It uses its own language called HCL (HashiCorp Configuration Language).

**Use Case:**
- Multi-cloud environment (AWS + Azure একসাথে)
- Open-source tool দরকার হলে
- Large team এ collaborative work

---

## 1. What is Infrastructure as Code (IaC)?

**বাংলা:** IaC মানে code দিয়ে infrastructure manage করা। Server, network, database — সব কিছু code এ define করা হয়। Code version control এ থাকে, review করা যায়, reuse করা যায়।

**English:** Infrastructure as Code is the process of managing and provisioning computing infrastructure through machine-readable configuration files rather than through manual configuration or interactive configuration tools.

**IaC ছাড়া (Manual):**
Developer → AWS Console → Click Click Click → EC2 বানালো
আবার লাগলে → আবার Click Click Click

**IaC দিয়ে:**
Developer → Code লিখলো → Run করলো → EC2 বানালো
আবার লাগলে → Same Code → Run করলো → Same EC2

---

## 2. What is AWS CloudFormation?

**বাংলা:** CloudFormation হলো AWS এর IaC service। YAML বা JSON template এ infrastructure define করো — CloudFormation সব বানায়, update করে, delete করে।

**English:** AWS CloudFormation is a service that helps you model and set up your AWS resources using templates. You create a template that describes all the AWS resources you want, and CloudFormation takes care of provisioning and configuring those resources for you.

**Key Concepts:**
| Term | মানে |
|------|------|
| Template | Infrastructure এর blueprint (YAML/JSON) |
| Stack | Template থেকে বানানো resources এর group |
| Change Set | Stack update করার আগে preview |
| Drift | Template আর actual resource এর পার্থক্য |

---

## 3. CloudFormation Template Structure

**বাংলা:** CloudFormation template এ বিভিন্ন section থাকে।

**English:** A CloudFormation template has several sections.

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Description: My first CloudFormation template

# ========== PARAMETERS ==========
Parameters:
  InstanceType:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium
    Description: EC2 instance type

  Environment:
    Type: String
    Default: dev
    AllowedValues:
      - dev
      - staging
      - prod

# ========== MAPPINGS ==========
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0abcdef1234567890
    ap-southeast-1:
      AMI: ami-0987654321fedcba

# ========== CONDITIONS ==========
Conditions:
  IsProduction: !Equals [!Ref Environment, prod]

# ========== RESOURCES (Required) ==========
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !FindInMap [RegionMap, !Ref AWS::Region, AMI]
      InstanceType: !Ref InstanceType
      Tags:
        - Key: Name
          Value: MyWebServer
        - Key: Environment
          Value: !Ref Environment

  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-project-bucket
      VersioningConfiguration:
        Status: Enabled

# ========== OUTPUTS ==========
Outputs:
  InstanceId:
    Description: EC2 Instance ID
    Value: !Ref MyEC2Instance

  BucketName:
    Description: S3 Bucket Name
    Value: !Ref MyS3Bucket
```

---

## 4. CloudFormation Intrinsic Functions

**বাংলা:** CloudFormation এ built-in functions আছে যেগুলো template এ dynamic values দেয়।

**English:** CloudFormation provides intrinsic functions to assign values to properties dynamically.

```yaml
# !Ref — resource বা parameter reference করো
InstanceType: !Ref InstanceTypeParam

# !GetAtt — resource এর attribute পাও
PublicIP: !GetAtt MyEC2Instance.PublicIp

# !Sub — string এ variable substitute করো
BucketName: !Sub "${Environment}-my-bucket"

# !Join — strings join করো
FullName: !Join ["-", [!Ref Environment, "bucket"]]

# !Select — list থেকে একটা select করো
SubnetId: !Select [0, !Ref SubnetList]

# !If — condition এর উপর based
InstanceType: !If [IsProduction, t2.large, t2.micro]

# !FindInMap — mapping থেকে value খোঁজো
AMI: !FindInMap [RegionMap, !Ref AWS::Region, AMI]

# !ImportValue — অন্য stack এর output import করো
VpcId: !ImportValue MyVPCStack-VpcId
```

---

## 5. CloudFormation Real Example — EC2 with VPC

**বাংলা:** একটা complete example — VPC, Subnet, Security Group, EC2 সব একসাথে।

**English:** A complete CloudFormation template creating a VPC with an EC2 instance.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: VPC with EC2 Instance

Parameters:
  KeyPairName:
    Type: AWS::EC2::KeyPair::KeyName
    Description: EC2 Key Pair

Resources:
  # VPC
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: MyVPC

  # Internet Gateway
  MyIGW:
    Type: AWS::EC2::InternetGateway

  # IGW Attach করো
  AttachIGW:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref MyIGW

  # Public Subnet
  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: PublicSubnet

  # Route Table
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  # Internet Route
  PublicRoute:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref MyIGW

  # Route Table Association
  SubnetRouteAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable

  # Security Group
  WebServerSG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Web Server Security Group
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

  # EC2 Instance
  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0abcdef1234567890
      InstanceType: t2.micro
      KeyName: !Ref KeyPairName
      SubnetId: !Ref PublicSubnet
      SecurityGroupIds:
        - !Ref WebServerSG
      UserData:
        Fn::Base64: |
          #!/bin/bash
          dnf -y install httpd
          systemctl enable httpd
          systemctl start httpd
          echo '<h1>Hello from CloudFormation!</h1>' > /var/www/html/index.html
      Tags:
        - Key: Name
          Value: WebServer

Outputs:
  WebServerPublicIP:
    Description: Web Server Public IP
    Value: !GetAtt WebServer.PublicIp

  WebsiteURL:
    Description: Website URL
    Value: !Sub "http://${WebServer.PublicIp}"
```

---

## 6. CloudFormation CLI Commands

**বাংলা:** CLI দিয়ে CloudFormation stack manage করা।

**English:** Managing CloudFormation stacks using AWS CLI.

```bash
# Stack বানাও
aws cloudformation create-stack \
  --stack-name my-stack \
  --template-body file://template.yaml \
  --parameters ParameterKey=Environment,ParameterValue=dev

# Stack update করো
aws cloudformation update-stack \
  --stack-name my-stack \
  --template-body file://template.yaml

# Stack delete করো
aws cloudformation delete-stack \
  --stack-name my-stack

# Stacks দেখো
aws cloudformation describe-stacks

# Stack events দেখো
aws cloudformation describe-stack-events \
  --stack-name my-stack

# Stack ready হওয়া পর্যন্ত wait করো
aws cloudformation wait stack-create-complete \
  --stack-name my-stack

# Change set বানাও (preview)
aws cloudformation create-change-set \
  --stack-name my-stack \
  --change-set-name my-changes \
  --template-body file://template.yaml

# Template validate করো
aws cloudformation validate-template \
  --template-body file://template.yaml
```

---

## 7. CloudFormation Tips and Tricks

**বাংলা:** CloudFormation ভালোভাবে use করার tips।

**English:** Best practices and tips for writing CloudFormation templates.

**Tip 1 — Parameters use করো:**
```yaml
# Hard-coded value এর বদলে parameter use করো
# Bad:
InstanceType: t2.micro

# Good:
Parameters:
  InstanceType:
    Type: String
    Default: t2.micro
```

**Tip 2 — Outputs export করো:**
```yaml
# অন্য stack এ use করার জন্য output export করো
Outputs:
  VpcId:
    Value: !Ref MyVPC
    Export:
      Name: !Sub "${AWS::StackName}-VpcId"
```

**Tip 3 — DependsOn use করো:**
```yaml
# Resource order নিশ্চিত করো
Resources:
  MyEC2:
    Type: AWS::EC2::Instance
    DependsOn: MyVPC  # VPC আগে বানাবে
```

**Tip 4 — DeletionPolicy use করো:**
```yaml
# Stack delete করলেও resource রাখো
Resources:
  MyDatabase:
    Type: AWS::RDS::DBInstance
    DeletionPolicy: Retain  # Delete করবে না
```

**Tip 5 — Stack নামকরণ:**
Good naming:
project-environment-component
myapp-prod-vpc
myapp-dev-ec2

**Tip 6 — Template validate করো deploy এর আগে:**
```bash
aws cloudformation validate-template \
  --template-body file://template.yaml
```

**Tip 7 — Change Set use করো:**
```bash
# Update এর আগে preview দেখো
aws cloudformation create-change-set \
  --stack-name my-stack \
  --change-set-name preview \
  --template-body file://new-template.yaml
```

---

## 8. What is Terraform?

**বাংলা:** Terraform হলো HashiCorp এর open-source IaC tool। HCL (HashiCorp Configuration Language) দিয়ে infrastructure define করা হয়। AWS, Azure, GCP সব cloud এ কাজ করে।

**English:** Terraform is an open-source infrastructure as code tool created by HashiCorp. It allows you to define both cloud and on-premises resources in human-readable configuration files using HCL (HashiCorp Configuration Language).

**Terraform এর Main Components:**

| Component | মানে |
|-----------|------|
| Provider | AWS, Azure, GCP connection |
| Resource | বানাতে চাওয়া resource |
| Variable | Dynamic values |
| Output | Result দেখানো |
| State | Current infrastructure এর record |
| Module | Reusable code block |

---

## 9. Terraform Basic Commands

**বাংলা:** Terraform এর সবচেয়ে গুরুত্বপূর্ণ commands।

**English:** Essential Terraform commands for managing infrastructure.

```bash
# Initialize করো (প্রথমবার)
terraform init

# Plan দেখো (কী হবে preview)
terraform plan

# Apply করো (actually বানাও)
terraform apply

# Destroy করো (সব delete)
terraform destroy

# State দেখো
terraform show

# Format করো
terraform fmt

# Validate করো
terraform validate

# Output দেখো
terraform output
```

---

## 10. Terraform Basic Example — EC2 Instance

**বাংলা:** Terraform দিয়ে EC2 instance বানানোর example।

**English:** A basic Terraform example to create an EC2 instance on AWS.

```hcl
# main.tf

# Provider configure করো
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "ap-southeast-1"
}

# VPC বানাও
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "MyVPC"
  }
}

# Subnet বানাও
resource "aws_subnet" "public_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "PublicSubnet"
  }
}

# Security Group বানাও
resource "aws_security_group" "web_sg" {
  name   = "web-server-sg"
  vpc_id = aws_vpc.my_vpc.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# EC2 Instance বানাও
resource "aws_instance" "web_server" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_subnet.id

  vpc_security_group_ids = [aws_security_group.web_sg.id]

  user_data = <<-EOF
    #!/bin/bash
    dnf -y install httpd
    systemctl enable httpd
    systemctl start httpd
    echo '<h1>Hello from Terraform!</h1>' > /var/www/html/index.html
  EOF

  tags = {
    Name = "WebServer"
  }
}

# Output দেখাও
output "public_ip" {
  value = aws_instance.web_server.public_ip
}
```

```hcl
# variables.tf
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "environment" {
  description = "Environment name"
  type        = string
  default     = "dev"
}
```

---

## 11. CloudFormation vs Terraform

**বাংলা:** দুইটার মধ্যে পার্থক্য বুঝলে কখন কোনটা use করবে সেটা clear হবে।

**English:** Understanding the differences helps you choose the right tool for your use case.

| Feature | CloudFormation | Terraform |
|---------|---------------|-----------|
| Provider | শুধু AWS | AWS, Azure, GCP সব |
| Language | YAML/JSON | HCL |
| Cost | Free | Free (Enterprise paid) |
| State Management | AWS manage করে | নিজে manage করতে হয় |
| Learning curve | সহজ (AWS জানলে) | একটু বেশি |
| Community | AWS community | বিশাল community |
| Multi-cloud | ❌ | ✅ |
| AWS Integration | Perfect | ভালো |
| Rollback | Automatic | Manual |
| Drift Detection | আছে | আছে |

**কখন CloudFormation:**
- শুধু AWS use করলে
- AWS এর সাথে tight integration দরকার হলে
- Automatic rollback দরকার হলে

**কখন Terraform:**
- Multi-cloud environment এ
- AWS + অন্য services একসাথে
- Open-source tool দরকার হলে
- Large community support দরকার হলে

---

## 12. IaC Best Practices

**বাংলা:** IaC use করার সময় যে best practices follow করা উচিত।

**English:** Best practices for writing and managing Infrastructure as Code.

**1. Version Control এ রাখো:**
```bash
git init
git add template.yaml
git commit -m "Add VPC CloudFormation template"
```

**2. Environment আলাদা করো:**
environments/
├── dev/
│   └── main.tf
├── staging/
│   └── main.tf
└── prod/
└── main.tf

**3. Sensitive data code এ রাখো না:**
```yaml
# Bad — password hard-coded
DBPassword: mypassword123

# Good — parameter store থেকে নাও
DBPassword: !Sub '{{resolve:ssm:/myapp/db-password}}'
```

**4. Modules/Nested Stacks use করো:**
modules/
├── vpc/
│   └── main.tf
├── ec2/
│   └── main.tf
└── rds/
└── main.tf

**5. Tags সব resource এ দাও:**
```yaml
Tags:
  - Key: Environment
    Value: !Ref Environment
  - Key: Project
    Value: MyApp
  - Key: Owner
    Value: DevTeam
```

**6. Template validate করো deploy এর আগে:**
```bash
# CloudFormation
aws cloudformation validate-template \
  --template-body file://template.yaml

# Terraform
terraform validate
terraform plan
```

---

## Key Takeaways

| Concept | এক কথায় |
|---------|---------|
| IaC | Code দিয়ে infrastructure manage |
| CloudFormation | AWS এর native IaC tool |
| Template | Infrastructure এর blueprint |
| Stack | Template থেকে বানানো resources |
| Change Set | Update এর preview |
| Terraform | Multi-cloud IaC tool |
| HCL | Terraform এর language |
| terraform init | Initialize করো |
| terraform plan | Preview দেখো |
| terraform apply | Actually বানাও |
| CFN vs TF | AWS only vs Multi-cloud |
| Best Practice | Version control, modules, tags |
