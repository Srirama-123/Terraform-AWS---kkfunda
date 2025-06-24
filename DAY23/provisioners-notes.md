# 📘 Terraform Provisioners 

---

## 🧠 What Are Provisioners?

> Provisioners in Terraform allow you to **execute scripts or commands** on a local or remote machine **after a resource is created or destroyed**.

They are used to **customize machines** (usually EC2) after Terraform has provisioned the infrastructure.

---

## 🎯 Why Use Provisioners?

* To install packages (e.g., Apache, Nginx)
* To copy configuration files
* To start or restart services
* To create logs or notify tools

---

## 🔍 Types of Provisioners

| Provisioner   | Runs On        | Purpose                            | SSH Needed? |
| ------------- | -------------- | ---------------------------------- | ----------- |
| `file`        | On EC2         | Uploads file to remote machine     | ✅ Yes       |
| `remote-exec` | On EC2         | Runs commands remotely on EC2      | ✅ Yes       |
| `local-exec`  | On Your Laptop | Runs commands on your local system | ❌ No        |

---

## 🛠 Common Use Case: EC2 Instance Setup

> Create an EC2 → Copy a script (file) → Run it remotely (remote-exec) → Log IP to your laptop (local-exec)

This is exactly what you did in the **combined lab**!

---

## 🔒 Important: Connection Block

For `file` and `remote-exec`, Terraform needs to connect to EC2 via SSH:

```hcl
connection {
  type        = "ssh"
  user        = "ec2-user"
  private_key = file("~/.ssh/your-key.pem")
  host        = self.public_ip
}
```

Without this, Terraform **cannot** perform remote provisioning.

---

## ⚠️ Best Practices

* **Don’t rely too much on provisioners** in production. Prefer cloud-init, Ansible, or AMIs.
* Use provisioners mainly for **testing**, **POCs**, or **initial setups**.
* Avoid using provisioners to manage large stateful configs (use config mgmt tools instead).

---

## 🧪 Provisioners Flow Diagram

```text
Terraform
   ↓
Creates EC2
   ↓
[Provisioners Start]
   ├── file         → Upload config to EC2
   ├── remote-exec  → Run bash commands (e.g., install Apache)
   └── local-exec   → Notify your system (e.g., log EC2 IP)
```

---

## 📌 Real-World Analogy

Imagine you're renting a brand-new apartment:

* 🛠 EC2 creation = Getting the flat
* 📦 `file` = Sending your luggage in advance
* 🧹 `remote-exec` = Calling a maid to setup and clean the room
* 📱 `local-exec` = Sending your new address to your friends

---

## 💬 FAQs Students May Ask

**Q: Can I use provisioners on any resource?**
✅ Yes, but they're mostly used on compute (EC2, VM, etc.).

**Q: What happens if a provisioner fails?**
By default, Terraform **fails the apply**. You can use `on_failure = continue` to skip.

**Q: Do I need a public IP for file or remote-exec?**
✅ Yes, unless you are in the same private network or using bastion/SSM.

---

## ✅ Summary

| Topic              | Description                         |
| ------------------ | ----------------------------------- |
| `file`             | Upload files to EC2                 |
| `remote-exec`      | Run commands on EC2 after creation  |
| `local-exec`       | Run commands on your local system   |
| `connection` block | Required for SSH-based provisioners |




