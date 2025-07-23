# Creating an Ubuntu 24 EC2 Instance in AWS Console for Kops

1. **Log in to AWS Console**
    - Go to [AWS Management Console](https://console.aws.amazon.com/).

2. **Navigate to EC2**
    - In the search bar, type **EC2** and select **EC2**.

3. **Launch Instance**
    - Click **Launch Instance**.

4. **Name and OS**
    - Enter a name (e.g., `kops-admin`).
    - Under **Application and OS Images (Amazon Machine Image)**, click **Browse more AMIs**.
    - Search for `Ubuntu 24.04` and select the latest official Ubuntu 24.04 LTS AMI.

5. **Instance Type**
    - Choose an instance type (e.g., `t3.medium` or as required).

6. **Key Pair**
    - Select an existing key pair or create a new one for SSH access.

7. **Network Settings**
    - Choose the appropriate VPC and subnet.
    - Allow SSH (port 22) in the security group.

8. **Storage**
    - Adjust storage size if needed (default is usually sufficient).

9. **Launch**
    - Click **Launch Instance**.

10. **Connect**
     - Once running, select the instance and click **Connect** for SSH instructions.

**Note:**  
- Ensure the instance is in the same region and VPC as your Kops cluster.
- Assign appropriate IAM roles if Kops needs AWS API access.

## Creating a `kop-user` IAM User with Administrative Access and Access Keys

1. **Open the IAM Console**
    - In the AWS Console, search for **IAM** and select it.

2. **Create User**
    - Click **Users** in the sidebar, then **Add users**.
    - Enter `kop-user` as the username.
    - Check **Provide user access to the AWS Management Console** if needed.
    - Check **Access key - Programmatic access** for CLI/API access.

3. **Set Permissions**
    - Choose **Attach policies directly**.
    - Search for and select **AdministratorAccess** (or a more restrictive policy as needed).

4. **Review and Create**
    - Click **Next**, review settings, and click **Create user**.

5. **Download Credentials**
    - Download the `.csv` file with the **Access key ID** and **Secret access key**.

> **Note:** Store the access keys securely. Use these credentials for configuring Kops and managing your Kubernetes cluster.

## Logging in to the EC2 Instance and Installing AWS CLI

1. **SSH into the EC2 Instance**
    - Use the key pair you selected earlier:
      ```sh
      ssh -i /path/to/your-key.pem ubuntu@<EC2_PUBLIC_IP>
      ```

2. **Switch to the Root User**
    - Run:
      ```sh
      sudo -i
      ```

3. **Update the System**
    - Update package lists:
      ```sh
      apt update && apt upgrade -y
      ```

4. **Install AWS CLI Using Snap**
    - Install Snap if not already present:
      ```sh
      apt install snapd -y
      ```
    - Install AWS CLI:
      ```sh
      snap install aws-cli --classic
      ```
    - Verify installation:
      ```sh
      aws --version
      ```
 5. **Configure AWS CLI with Access Keys**
    - Run the following command and enter your **Access key ID**, **Secret access key**, default region, and output format when prompted:
        ```sh
          aws configure
       ```
    ## Generating SSH Key Pair on the EC2 Instance

    1. **Generate SSH Key Pair**
        - On the EC2 instance, run:
          ```sh
          ssh-keygen
          ```
        - Press `Enter` to accept the default file location and optionally set a passphrase.

    2. **Verify Keys**
        - The public key will be at `~/.ssh/id_rsa.pub` and the private key at `~/.ssh/id_rsa`.
        ```sh
        ls .ssh/
        ```
    > **Tip:** Use the public key (`id_rsa.pub`) to configure access to other servers or services as needed.
## Installing Kops on the EC2 Instance
1. **Download Kops Binary**
    - Run the following command to download the latest Kops binary:
      ```sh
      curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
      chmod +x kubectl
      sudo mv kubectl /usr/local/bin/
     ```
     
3. **Install kubectl**
    - Download the latest `kubectl` binary:
      ```sh
      curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
      
      chmod +x kubectl
      
      sudo mv kubectl /usr/local/bin/
      ```
      > **Note:** If you prefer to install `kubectl` using a package manager, you can use the following command:

      ```sh
      sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
      ```     
    ## Creating an S3 Bucket Using the AWS Console

    1. **Open the S3 Console**
        - In the AWS Console, search for **S3** and select **S3**.

    2. **Create Bucket**
        - Click the **Create bucket** button.

    3. **Configure Bucket Settings**
        - Enter a unique **Bucket name** (e.g., `kops-state-store-<unique-suffix>`).
        - Select the **AWS Region** where your resources are located.

    4. **Bucket Options**
        - Leave default settings unless you have specific requirements (e.g., versioning, encryption).
        - For Kops, you can leave **Block all public access** enabled.

    5. **Create the Bucket**
        - Scroll down and click **Create bucket**.

    > **Note:**  
    > - Remember the bucket name; you will use it as your Kops state store (e.g., `s3://kops-state-store-<unique-suffix>`).

    ## Creating a Route 53 Hosted Zone with a Purchased Domain

    1. **Purchase a Domain (if not already purchased)**
        - Go to the [Route 53 Console](https://console.aws.amazon.com/route53/).
        - Click **Registered domains** in the sidebar.
        - Click **Register domain** and follow the prompts to purchase your desired domain.

    2. **Create a Hosted Zone**
        - In the Route 53 Console, click **Hosted zones** in the sidebar.
        - Click **Create hosted zone**.
        - Enter your domain name (e.g., `vpro.cloud-ops.shop`) in the **Domain name** field.
        - Leave **Type** as **Public hosted zone**.
        - Click **Create hosted zone**.

    3. **Update Domain Name Servers (if domain is registered outside AWS)**
        - If you purchased your domain from a third-party registrar, copy the four **NS (Name Server)** records from the hosted zone.
        - Log in to your domain registrar and update the domain's name servers to the values provided by Route 53.

    4. **Verify Hosted Zone**
        - After updating the name servers, DNS propagation may take some time (up to 48 hours).
        - You can verify by running:
          ```sh
          dig NS example.com
          ```
        - The output should show the Route 53 name servers.

    > **Note:**  
    > - Use this hosted zone for your Kops cluster DNS (e.g., `cluster.example.com`).

    ## Setting Up Hosted Zones for Your Domain in GoDaddy

    If your domain is registered with GoDaddy, follow these steps to configure DNS for use with AWS Route 53:

    1. **Log in to GoDaddy**
        - Go to [GoDaddy.com](https://www.godaddy.com/) and sign in to your account.

    2. **Access Your Domain**
        - Click on your username (top right) and select **My Products**.
        - Under **Domains**, find your domain and click **DNS** or **Manage DNS**.

    3. **Update Name Servers**
        - In the DNS Management page, scroll to the **Nameservers** section.
        - Click **Change** or **Edit Nameservers**.
        - Select **Enter my own nameservers (advanced)**.
        - Enter the four NS (Name Server) records provided by your AWS Route 53 hosted zone.
        - Click **Save** or **OK** to apply the changes.

    4. **Wait for DNS Propagation**
        - DNS changes may take up to 48 hours to propagate globally.

    > **Tip:**  
    > You can verify the update by running `dig NS yourdomain.com` or using online DNS lookup tools.

    Now your GoDaddy domain will use AWS Route 53 for DNS management.
    ## Creating the Kubernetes Cluster with Kops

    1. **Run the Kops Create Cluster Command**

        On your EC2 instance, execute the following command (replace values as needed):

        ```sh
        kops create cluster \
          --name=vpro.cloud-ops.shop \
          --state=s3://kopskabucket \
          --zones=us-east-1a,us-east-1b \
          --node-count=2 \
          --node-size=t3.small \
          --control-plane-size=t3.medium \
          --dns-zone=vpro.cloud-ops.shop \
          --node-volume-size=12 \
          --control-plane-volume-size=12 \
          --ssh-public-key ~/.ssh/id_ed25519.pub
        ```

    2. **Apply the Cluster Configuration**

        To provision the cluster resources, run:

        ```sh
        kops update cluster --name=vpro.cloud-ops.shop --state=s3://kopskabucket --yes --admin
        ```

    > **Note:**  
    > - The `--admin` flag creates a temporary admin kubeconfig credential.
    > - Monitor the progress in the AWS Console (EC2, Auto Scaling, etc.).
    > - After completion, use `kubectl get nodes` to verify your cluster.

    3. **Validate the Cluster**

        After the cluster is created, validate it with:

        This command checks the status of your cluster and ensures all nodes are ready.

   ```sh
    kops validate cluster --name=vpro.cloud-ops.shop --state=s3://kopskabucket
   ``` 
**SUMMARY AND NOTE:**
- Launch an EC2 instance with Ubuntu 24.04.
- Create an IAM user `kop-user` with administrative access and access keys.
- Install AWS CLI and configure it with the access keys.
- Generate an SSH key pair on the EC2 instance.
- Install Kops and kubectl on the EC2 instance.
- Create an S3 bucket for Kops state storage.
- Create a Route 53 hosted zone for your domain.
- Set up DNS in GoDaddy if your domain is registered there.
- Create a Kubernetes cluster using Kops with the specified configurations.
- Validate the cluster to ensure it is running correctly.
- We need to make ready the domain and DNS settings before creating the cluster to ensure proper functionality.
- We use Route 53 for DNS management, which is integrated with AWS services.
- We create Hosted Zones in Route 53 for the domain, allowing Kops to manage DNS records for the Kubernetes cluster.
- Then 4 NS records are set in GoDaddy to point to the Route 53 hosted zone.
-Route 53 is used for DNS management, and the domain is set up to point to the Route 53 hosted zone.
- The cluster is created with Kops, specifying the state store, zones, node sizes, and other configurations.
