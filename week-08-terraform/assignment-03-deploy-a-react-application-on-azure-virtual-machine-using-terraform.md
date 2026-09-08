# Assignment 3 — Deploy a React Application on Azure Using Terraform

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision the required Azure infrastructure and automatically deploy the `my-react-app` React application on an Azure Linux virtual machine using a `cloud-init.sh` deployment script passed to the VM through `custom_data`.

You will verify the automated deployment through SSH, confirm that Nginx is running, access the React application through the VM public IP, and destroy the Terraform-managed resources after testing.

---

# Task 0 — Set Up and Verify the Terraform and Azure CLI Environment

## Goal

Prepare your local environment for Terraform deployment by installing Terraform, Azure CLI, and the HashiCorp Terraform extension in VS Code, signing in to your Azure account, and confirming that all required tools are working correctly.

## Evidence

### Screenshot 1 — Terraform Version

![Terraform Version](screenshots/Wk-08-Ass-1-scrn-1.png)

---

### Screenshot 2 — Azure CLI Version

![Azure CLI Version](screenshots/Wk-08-Ass-1-scrn-2.png)

---

### Screenshot 3 — HashiCorp Terraform Extension

![HashiCorp Terraform Extension](screenshots/Wk-08-Ass-1-scrn-3.png)

---

# Task 1 — Create a New Terraform Project and Define the Infrastructure

## Goal

Create a new Terraform project and define the complete Azure infrastructure required to host the React application using the official Terraform Registry documentation.

The `terraform-react-azure` project must contain:

```text
terraform-react-azure/
├── main.tf
└── cloud-init.sh
```

The Terraform configuration must include:

- Terraform and AzureRM provider configuration
- Resource group
- Virtual network and subnet
- Network Security Group
- SSH rule for TCP port `22`
- HTTP rule for TCP port `80`
- Public IP address
- Network interface
- Linux virtual machine
- `custom_data` configuration referencing `cloud-init.sh`
- Public IP output

The `cloud-init.sh` file must contain the complete automated React application deployment workflow based on the repository instructions.

## Evidence

### Screenshot 4 — Provider, Resource Group, and Network Security Group

![Provider, Resource Group, and Network Security Group](screenshots/Wk-08-Ass-3-scrn-4.png).

---

### Screenshot 5 — Linux Virtual Machine and `custom_data`

A screenshot of VS Code showing the Linux virtual machine configuration, including the `custom_data` configuration, in `main.tf`.

Ensure that passwords, private keys, account IDs, access tokens, and other sensitive information are hidden.

![Linux Virtual Machine and `custom_data`](screenshots/Wk-08-Ass-3-scrn-5.png)

---

### Screenshot 6 — Completed `cloud-init.sh`

A screenshot of VS Code showing the completed `cloud-init.sh` deployment script.

Ensure that no passwords, Azure credentials, access tokens, SSH private keys, or other sensitive information are visible.

![Completed `cloud-init.sh`](screenshots/Wk-08-Ass-3-scrn-6.png)

---

### Screenshot 7 — Public IP Output Block

A screenshot of VS Code showing the public IP `output` block in `main.tf`.

![Public IP Output Block](screenshots/Wk-08-Ass-3-scrn-7.png)

---

# Task 2 — Initialize Terraform

## Goal

Initialize the Terraform working directory and download the required provider components.

## Evidence

### Screenshot 8 — Terraform Initialization

A screenshot of the terminal showing successful `terraform init` output.

![Terraform Initialization](screenshots/Wk-08-Ass-3-scrn-8.png)

---

# Task 3 — Plan and Apply the Configuration

## Goal

Review the Terraform execution plan and provision the Azure infrastructure.

## Evidence

### Screenshot 9 — Terraform Plan

A screenshot showing the Terraform plan summary and the proposed resources.

![Terraform Plan](screenshots/Wk-08-Ass-3-scrn-9.png)

---

### Screenshot 10 — Terraform Apply

A screenshot showing successful `terraform apply` completion.

![Terraform Apply](screenshots/Wk-08-Ass-3-scrn-10.png)

---

### Screenshot 11 — VM Public IP Output

A screenshot showing the VM public IP address returned by `terraform output`.

![VM Public IP Output](screenshots/Wk-08-Ass-3-scrn-11.png)

## VM Public IP Address

Record the public IP address displayed by `terraform output`.

**VM Public IP Address:** 20.84.52.125

---

# Task 4 — Verify the Automated Deployment

## Goal

Connect to the Azure Linux virtual machine and confirm that the cloud-init/user data deployment script completed successfully.

## Evidence

### Screenshot 12 — SSH Connection and Completed React Deployment

A screenshot of the SSH terminal showing a successful connection to the Azure VM and evidence that the React application deployment completed.

![SSH Connection and Completed React Deployment](screenshots/Wk-08-Ass-3-scrn-12.png)

---

### Screenshot 13 — Nginx Service Status

A screenshot of the terminal showing that the Nginx service is running successfully.

![Nginx Service Status](screenshots/Wk-08-Ass-3-scrn-13.png)

---

# Task 5 — Verify the React Application Deployment

## Goal

Confirm that the automatically deployed React application is publicly accessible and functioning correctly.

## Evidence

### Screenshot 14 — React Application in the Browser

A screenshot of the browser showing the deployed React application successfully loaded using the Azure VM public IP.

Ensure that the Azure VM public IP is visible in the browser address bar.

![React Application in the Browser](screenshots/Wk-08-Ass-3-scrn-14.png)

---

# Task 6 — Destroy the Resources

## Goal

Remove all Azure resources created by Terraform after completing the application deployment and verification.

## Evidence

### Screenshot 15 — Terraform Destroy

A screenshot of the terminal showing successful `terraform destroy` completion.

![Terraform Destroy](screenshots/Wk-08-Ass-3-scrn-15.png)

---

# Submission Instructions

- Complete Tasks 0–6 in sequence.
- Include all 15 required screenshots exactly as specified.
- Ensure that your full name is visible in the required screenshots.
- Record the VM public IP address under Task 3.
- Ensure that the submitted evidence clearly matches the required task outputs.
- Include `main.tf` and `cloud-init.sh` in your GitHub submission.
- Do not expose passwords, SSH private keys, account IDs, access tokens, Azure credentials, or other sensitive information.
- Do not store secrets inside `cloud-init.sh`.
- Review all screenshots and project files carefully before submitting through GitHub.

---

# Completion Checklist

- [ ] Installed Terraform and verified it using `terraform version`
- [ ] Installed Azure CLI and verified it using `az version`
- [ ] Signed in to Azure and confirmed the correct subscription
- [ ] Installed and enabled the HashiCorp Terraform extension in VS Code
- [ ] Created the `terraform-react-azure` project
- [ ] Created `main.tf`
- [ ] Defined the Terraform and AzureRM provider configuration
- [ ] Defined the resource group
- [ ] Defined the virtual network and subnet
- [ ] Defined the Network Security Group
- [ ] Configured SSH and HTTP rules
- [ ] Defined the public IP and network interface
- [ ] Created `cloud-init.sh`
- [ ] Reviewed the React application repository instructions
- [ ] Created the complete deployment workflow inside `cloud-init.sh`
- [ ] Defined the Linux virtual machine
- [ ] Connected `cloud-init.sh` to the VM using `custom_data`
- [ ] Used `file()` and `base64encode()` correctly
- [ ] Added the Terraform public IP output
- [ ] Completed `terraform init` successfully
- [ ] Reviewed the Terraform execution plan
- [ ] Completed `terraform apply` successfully
- [ ] Recorded the VM public IP
- [ ] Connected to the VM through SSH
- [ ] Verified that the automated deployment completed successfully
- [ ] Verified that Nginx is running
- [ ] Verified the React application through the browser
- [ ] Completed `terraform destroy` successfully
- [ ] Captured all 15 required screenshots
- [ ] Confirmed that my full name is visible in the required screenshots
- [ ] Checked that no passwords, keys, account IDs, access tokens, or other sensitive information are exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory), focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations through hands-on experience.

---

## Resources

- React Application Repository: [https://github.com/pravinmishraaws/my-react-app](https://github.com/pravinmishraaws/my-react-app)
- DMI Official Website: [https://dmi.pravinmishra.com](https://dmi.pravinmishra.com)
- University: [https://university.pravinmishra.com](https://university.pravinmishra.com)
- Discord Community: [https://discord.pravinmishra.com](https://discord.pravinmishra.com)
- Blog: [https://dmi.pravinmishra.com/blog](https://dmi.pravinmishra.com/blog)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra on LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory on LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of the DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
