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
1. Download this role to the Ansible control plane from Ansible Galaxy by running the following command:
    ```sh
    ansible-galaxy role install rathnaarun77.nkp_ansible
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
        - rathnaarun77.nkp_ansible
      environment:
        ANSIBLE_HOST_KEY_CHECKING: "False"
      vars:
        image_url: ""  # Download URL for NKP Rocky node OS image
        SSH_PUBLIC_KEY: ""  # SSH public key of Ansible control node
        nkp_url: ""  # NKP binary download link for Linux
        NKP_VERSION: ""  # NKP version to install
        CLUSTER_NAME: ""  # NKP cluster name
        NUTANIX_USER: ""  # Prism Central username
        NUTANIX_PASSWORD: ''  # Keep the password enclosed between single quotes
        NUTANIX_ENDPOINT: ""  # Prism Central IP address
        NUTANIX_PORT: "9440"  # Prism Central port
        LB_IP_RANGE: ""  # Load balancer IP range
        CONTROL_PLANE_ENDPOINT_IP: ""  # Kubernetes VIP
        NUTANIX_MACHINE_TEMPLATE_IMAGE_NAME: ""  # NKP Rocky image name
        NUTANIX_PRISM_ELEMENT_CLUSTER_NAME: ""  # Prism Element cluster name
        NUTANIX_SUBNET_NAME: ""  # Example: primary
        NUTANIX_STORAGE_CONTAINER_NAME: ""  # Prism storage container 
        DOCKER_USERNAME: "" #Enter your docker username
        DOCKER_PASSWORD: "" #Enter your docker password
    ```

    > ⚠️ **Note:** All the variables are mandatory.

4. Finally, to trigger the playbook, run the following command:
    ```sh
    ansible-playbook -i inventory.ini playbook.yml
    ```

5. Once the playbook completes without any errors, you will get the NKP dashboard URL and the login credentials as output of the final task.

Disclaimer:

The views and opinions expressed in this repository are my own and do not necessarily reflect those of any company or organization. The information provided is based on personal experience and research. It is presented as-is without any warranties. For official guidance, please refer to the official documentation or support channels.

## License
MIT
