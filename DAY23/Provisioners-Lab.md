

# 🧪 Combined Provisioners Lab — EC2 with `file`, `remote-exec`, and `local-exec`

---

## 🎯 Objective

* ✅ Launch an EC2 instance
* ✅ Upload a file using `file` provisioner
* ✅ Install Apache using `remote-exec`
* ✅ Log the instance's public IP locally using `local-exec`

---

## 📁 Project Structure

```bash
combined-lab/
├── main.tf
├── variables.tf
├── terraform.tfvars
├── app.txt         # File to upload
```

---

## 1️⃣ `variables.tf`

```hcl
variable "ami_id" {
  default = "ami-0c55b159cbfafe1f0"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "key_name" {
  description = "AWS EC2 Key Pair name"
  type        = string
}

variable "subnet_id" {
  description = "Public subnet for EC2"
  type        = string
}
```

---

## 2️⃣ `terraform.tfvars`

```hcl
key_name  = "kkfunda-key"
subnet_id = "subnet-xxxxxxxx"
```

(Replace with actual key pair and subnet)

---

## 3️⃣ `app.txt`

```txt
Welcome to KK Funda provisioner demo!
Apache will be installed via Terraform.
```

---

## 4️⃣ `main.tf` — Full EC2 with All 3 Provisioners

```hcl
resource "aws_instance" "provision_demo" {
  ami                         = var.ami_id
  instance_type               = var.instance_type
  key_name                    = var.key_name
  subnet_id                   = var.subnet_id
  associate_public_ip_address = true

  # SSH connection setup
  connection {
    type        = "ssh"
    user        = "ec2-user"
    private_key = file("~/.ssh/kkfunda-key.pem")  # Use correct key path
    host        = self.public_ip
  }

  # Step 1: Upload app.txt to EC2
  provisioner "file" {
    source      = "app.txt"
    destination = "/home/ec2-user/app.txt"
  }

  # Step 2: Install Apache
  provisioner "remote-exec" {
    inline = [
      "sudo yum update -y",
      "sudo yum install httpd -y",
      "sudo systemctl start httpd",
      "sudo systemctl enable httpd"
    ]
  }

  # Step 3: Log IP locally
  provisioner "local-exec" {
    command = "echo EC2 Instance created at: ${self.public_ip} >> ec2_ips.log"
  }

  tags = {
    Name = "kkfunda-combined-lab"
  }
}
```

---

## 🚀 How to Run the Lab

1. Make sure your key file (`.pem`) is in place
2. Run these commands:

```bash
terraform init
terraform apply
```

3. Type `yes` to confirm.

---

## ✅ Expected Results

| Task                 | Result                                    |
| -------------------- | ----------------------------------------- |
| EC2 launched         | One `t2.micro` instance                   |
| File copied          | `app.txt` on EC2 at `/home/ec2-user/`     |
| Apache installed     | You can visit `http://<public-ip>`        |
| Local IP log created | File `ec2_ips.log` contains new public IP |

---

## 🔍 Verify from Terminal

SSH into instance:

```bash
ssh -i ~/.ssh/kkfunda-key.pem ec2-user@<public-ip>
```

Check the uploaded file:

```bash
cat /home/ec2-user/app.txt
```

Check Apache:

```bash
curl http://<public-ip>
```

---

## 🧹 Clean Up

```bash
terraform destroy
```

---

