# Guide to create web application and database (Cloudsec Asgmt 2)
## Step 1 - Connect to EC2 Instance (via SSH)
To download the `vockey.pem` private key file in the AWS Academy Cloud Foundations environment, follow these steps:

1. **Go to AWS Academy Sandbox Workbench**
2. **Retrieve the `vockey.pem` File:**
    - Locate the **Details** button above the terminal, on the left of AWS button.
    - In the dropdown menu, click **Show**.
    - Click on the **Download PEM** button to download the `vockey.pem` file to your local machine.
3. **Connect to Your EC2 Instance:**
    - Use the following SSH command to connect to your EC2 instance:Replace `your-ec2-public-dns` with the Public DNS address of your EC2 instance, which can be found in the EC2 console under instance details.
        
    ```bash
    ssh -i "yourpath\labsuser.pem" ec2-user@your-ec2-public-ip
    ```
        
    
    Example
    ```bash
    ssh -i "C:\Users\Tan Jia Quan\Downloads\labsuser.pem" ec2-user@44.212.19.19
    ```

## Step 2 - Install Web Application (Flask) & Database (Maria DB)
1. **Install Flask**
    
    <aside>
    ⚠️ Run the following command line by line
    </aside>
    
    ```bash
    sudo dnf update -y
    python3 --version
    sudo dnf install python3 -y
    sudo dnf install python3-pip -y
    pip install flask
    sudo dnf install git -y # Install Git
    sudo dnf install mariadb105-server -y # Install MariaDB (MySQL Compatible)
    pip3 install mysql-connector-python # Install a Database Connector
    ```
    
2. **Start MariaDB**
    
    ```bash
    sudo systemctl start mariadb
    ```
    
3. **Enable MariaDB to start on boot**
    
    ```bash
    sudo systemctl enable mariadb
    ```
    
4. **Secure MariaDB:**  *This is very important.*
    
    ```bash
    sudo mysql_secure_installation
    ```
    
    This runs a script that helps you set a root password, remove anonymous users, disallow remote root login, and remove the test database.  *Follow the prompts carefully and answer yes (Y) to all the security questions.*  This is crucial for securing your database.  Write down the root password you set!
    
    - Press Enter
    - Switch to unix_socket authentication [Y/n] → y
    - Change the root password [Y/n]? → y
        - root pw: password
    - Remove anonymous users? [Y/n] →  y
    - Disallow root login remotely? [Y/n] → y
    - Remove test database and access to it? [Y/n] → y
    - Reload privilege tables now? [Y/n] → y
5. **Clone Web Application Source code from GitHub:**
    
    ```bash
    git clone https://github.com/jiaquantan/webapp.git
    ```
    
6. **Connect to MariaDB:**
    
    ```bash
    cd webapp/
    mysql -u root -p
    ```
    
7. **Run SQL script:**
    
    Source the file from within the MariaDB prompt:
    
    ```bash
    SOURCE asgmtdb.sql;
    password
    ```
    
8. **Verify Tables Creation:**
    
    ```sql
    USE AsgmtDB;
    SHOW TABLES;
    SELECT * FROM Suppliers;
    SELECT * FROM Employees;
    SELECT * FROM Customers;
    ```
    
    ```sql
    exit
    ```

9. **Change Security Group Configuration (AWS Management Console)**

   Go back to **_AWS Management Console EC2_**, click **_Instances_**, select **_asgmt2_**, go to **_Security_** tab, click the **_launch-wizard-1_** security group link, click **_Edit inbound rules_**, click **_Add rule_**, configure the inbound rules as following as shown in Figure 1.4. 

    Type: **_Custom TCP_** 
    
    Port Range: **_5000_** 
    
    Source: **_Anywhere-IPv4_** (To allow http connection from the internet) 
    
    Click **_Save rules_**! 

11. **Run the web application:**
    
    In `webapp` directory, run the web app:
    
    ```bash
    cd webapp
    python3 app.py
    ```
    
    This will start the Flask development server.  You should see output similar to:
    
    ```python
     * Serving Flask app "app"
     * Debug mode: on
    WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Running on all addresses (0.0.0.0).
     * Running on http://127.0.0.1:5000
     * Running on http://172.31.22.84:5000
    Press CTRL+C to quit
     * Restarting with stat
     * Debugger is active!
     * Debugger PIN: 485-924-721
    ```
            
12. **Access the web app:**
    - Replace `ec2publicipv4addr` with yout ec2 instance’s public ipv4
    - Type the below URL into your browser
    
    ```bash
    http://ec2publicipv4addr:5000 (e.g. http://44.212.19.19:5000)
    ```
    
    <aside>
    ⚠️ Make sure to include :5000 in the URL
    </aside>
