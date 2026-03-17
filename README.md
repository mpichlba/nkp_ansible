# Ansible Role to Deploy NKP Management Cluster on Nutanix

## Overview
This role automates the deployment of a Nutanix Kubernetes Platform (NKP) management cluster in a Nutanix environment.

## What is NKP?
NKP enables organizations to easily overcome Kubernetes Day 2 operational barriers, such as security, observability, reliability, upgradeability, backup and restore, policy management, and governance. This saves time and resources, enabling organizations to operationalize production-ready cloud-native environments in minutes rather than weeks or months.

<img width="1609" alt="nkp_intro" src="https://github.com/user-attachments/assets/f8cbddf5-ac04-4f19-82f7-855487cc2b06" />

For more details and NKP pre-requisites, refer to the NKP platform guide.
[NKP platform guide](https://portal.nutanix.com/page/documents/details?targetId=Nutanix-Kubernetes-Platform-v2_14:Nutanix-Kubernetes-Platform-v2_14)

## Tasks performed by this role:

### 1. NKP Node OS Image Check and Download
- Verifies if the NKP Rocky Linux image is available in Nutanix Prism Central.
- Downloads the image if it is not present.

### 2. Bastion VM Deployment
- Deploys a new bastion VM on the target Nutanix cluster using the downloaded NKP Rocky image.
- Configures the bastion VM with required dependencies using cloud-init.

### 3. NKP Binaries Setup
- Downloads and extracts the required NKP binaries for the specified version on the bastion VM.

### 4. NKP Management Cluster Deployment
- Creates the NKP management cluster using settings defined in the input variables.

### 5. Cluster Access Information
- Once the cluster is successfully created, it prints the dashboard URL along with credentials for login.

## Usage

### Pre-requisites:
Since we are deploying in a Nutanix environment, install the `nutanix.ncp` ansible collection by running the below command on your control node:  

    ansible-galaxy collection install nutanix.ncp

### Steps:
1. Clone this repository to your Ansible control plane:
    ```sh
    git clone <repo-url>
    cd nkp_ansible
    ```

2. Create or download the `inventory.ini` file from this repo and pass it while you run the Ansible playbook, it just contains the localhost so no modifications required.
   ```yaml
   [localhost]
   localhost ansible_connection=local
   ```

3. Below is a sample `playbook.yml` file utilising this role:
    ```yaml
    ---
    - hosts: all
      gather_facts: no
      roles:
        - nkp_ansible
      environment:
        ANSIBLE_HOST_KEY_CHECKING: "False"
      vars_files:
        - vars.yml  # Load variables from separate file
    ```

    > ⚠️ **Note:** All the variables are mandatory. Variables can be stored in a `vars.yml` file and referenced using `vars_files` (recommended) or defined inline in the playbook.

4. Finally, to trigger the playbook, run the following command:
    ```sh
    ansible-playbook -i inventory.ini playbook.yml
    ```

5. Once the playbook completes without any errors, you will get the NKP dashboard URL and the login credentials as output of the final task.

Disclaimer:

The views and opinions expressed in this repository are my own and do not necessarily reflect those of any company or organization. The information provided is based on personal experience and research. It is presented as-is without any warranties. For official guidance, please refer to the official documentation or support channels.

## License
MIT
