Here’s a **Terraform script** to provision an **EC2 instance on AWS** with basic security group settings.  

### **Steps to Use the Terraform Script**  
1. Install [Terraform](https://developer.hashicorp.com/terraform/downloads).  
2. Configure AWS credentials (`aws configure`).  
3. Save the script as `main.tf`.  
4. Run:  
   ```sh
   terraform init
   terraform apply -auto-approve
   ```  

---

### **Terraform Script (`main.tf`)**
```hcl
provider "aws" {
  region = "us-east-1"  # Change as needed
}

resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI (Update if needed)
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform-EC2"
  }

  security_groups = [aws_security_group.allow_ssh.name]
}

resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow SSH inbound traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # Restrict this for security
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

output "instance_ip" {
  value = aws_instance.web_server.public_ip
}
```

---

### **What This Script Does**
- **Deploys an EC2 instance** (Amazon Linux 2, `t2.micro`).
- **Creates a security group** allowing SSH access (`port 22`).
- **Outputs the public IP** of the instance.

Do you need additional features like provisioning with **Ansible**, attaching an **EBS volume**, or deploying **Docker**? 🚀
