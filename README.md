# Configure Load Balancing

Another `ornottodo` concern is the reliability of TODO web app. To address it, you have already updated the script to deploy 2 web servers instead of 1. However, now you have to somehow balance traffic between them.

In this task, you will set up Azure Load Balancer to distribute traffic between 2 VMs. 

## Prerequisites

Before completing any task in the module, make sure that you followed all the steps described in the **Environment Setup** topic, in particular: 

1. Ensure you have an [Azure](https://azure.microsoft.com/en-us/free/) account and subscription.

2. Create a resource group called *“mate-resources”* in the Azure subscription.

3. In the *“mate-resources”* resource group, create a storage account (any name) and a *“task-artifacts”* container.

4. Install [PowerShell 7](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7.4) on your computer. All tasks in this module use PowerShell 7. To run it in the terminal, execute the following command: 
    ```
    pwsh
    ```

5. Install [Azure module for PowerShell 7](https://learn.microsoft.com/en-us/powershell/azure/install-azure-powershell?view=azps-11.3.0): 
    ```
    Install-Module -Name Az -Repository PSGallery -Force
    ```
If you are a Windows user, before running this command, please also run the following: 
    ```
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
    ```

6. Log in to your Azure account using PowerShell:
    ```
    Connect-AzAccount -TenantId <your Microsoft Entra ID tenant id>
    ```

## Requirements

In this task, you will need to update the existing PowerShell script `task.ps1`. The original script deploys the following resources: 

- virtual network with 2 subnets;
- `webserver-1` and `webserver-2` virtual machines — used to host the todo web app. They are deployed in a subnet `web`, without public IP;
- `jumpbox` virtual machine, used as a dedicated virtual machine for management of `webserver`. It is deployed to a separate subnet, has a public IP, and allows connecting to it through SSH; 
- `or.nottodo` private DNS zone, which is already linked to the virtual network;
- `todo.or.not` a DNS record in the private DNS zone that points to IP `10.20.30.62`.

In this task, you need to configure load balancing for the web servers. Specifically, you need to deploy a load balancer, configure it, and point domain `todo.or.nottodo` in the private DNS zone to its Frontend IP and create a DNS record in it: 

1. Review the script `task.ps1`.  

2. Add deployment of the private load balancer to the script: 

- Use the [quickstart](https://learn.microsoft.com/en-us/azure/load-balancer/quickstart-load-balancer-standard-internal-powershell#create-load-balancer) to get all nessesary commands;
- Loadbalancer should be deployed into the subnet `webservers`;
- Loadbalancer frontend IP should be set to `10.20.30.62`;
- Your backend uses `port 8080` to run the web app — use this port in both health probe and load balancing rule configuration;
- Use `80` as a frontend port in the load balancing rule.

3. Uncomment (and edit if needed) code at the end of `task.ps1`, which adds VMs to the load balancer backend pool;

4. When the script is ready, run it to deploy resources to your subscription;

5. To test yourself, connect to the `jumbox` VM with SSH and test with curl; if you can get to the todo web app by its domain name `todo.or.nottodo` — the app should be accessible on the following URL: `http://todo.or.nottodo/`; 

6. Run artifacts generation script `scripts/generate-artifacts.ps1`;

7. Test yourself using the script `scripts/validate-artifacts.ps1`;

8. Make sure that changes to both `task.ps1` and `result.json` are committed to the repo, and submit the solution for review; 

9. When the solution is validated, delete the resources you deployed with the PowerShell script — you won't need them for the next tasks;


## How to Complete Tasks in This Module 

Tasks in this module are relying on 2 PowerShell scripts: 

- `scripts/generate-artifacts.ps1` generates the task “artifacts” and uploads them to cloud storage. An “artifact” is evidence of a task completed by you. Each task will have its own script, which will gather the required artifacts. The script also adds a link to the generated artifact in the `artifacts.json` file in this repository — make sure to commit changes to this file after you run the script. 
- `scripts/validate-artifacts.ps1` validates the artifacts generated by the first script. It loads information about the task artifacts from the `artifacts.json` file.

Here is how to complete tasks in this module:

1. Clone task repository;

2. Make sure you completed the steps, described in the Prerequisites section;

3. Complete the task described in the Requirements section;

4. Run `scripts/generate-artifacts.ps1` to generate task artifacts. The script will update the file `artifacts.json` in this repo;

5. Run `scripts/validate-artifacts.ps1` to test yourself. If tests are failing — follow the recommendations from the test script error message to fix or re-deploy your infrastructure. When you are ready to test yourself again — **re-generate the artifacts** (step 4) and re-run tests again; 

6. When all tests will pass — commit your changes and submit the solution for review. 

Pro tip: if you are stuck with any of the implementation steps, run `scripts/generate-artifacts.ps1` and `scripts/validate-artifacts.ps1`. The validation script might give you a hint on what to do.  
