# Active-Directory-Project
An Active Directory project created to simulate an environment to analyze security vulnerabilities and response mechanisms. An Active Directory infrastructure was set up utilizing virtual machines to mirror a real-world network. I integrated Splunk to collect and visualize log data to monitor and analyze network activities. Additionally, I employed Kali Linux to conduct brute force attacks, simulating potential threats. This project aimed to understand the effectiveness of security measures and the capabilities of monitoring tools in identifying and mitigating unauthorized access attempts

## Objective

This Active Directory Project aims to establish a monitored active directory environment for simulating domain users within and sending out brute force attacks toward them. The primary focus was to send out an attack once information was "compromised" and then use the telemetry that was returned to figure out where exactly was being targeted due to irregular behavior and patterns to mimic a real-world attack/breach. This hands-on experience was created to deepen the understanding of active directory, brute force attacks, and virtual machines.

### Skills Learned

- Configuring Active Directory within multiple virtual machines 
- Development of critical thinking and problem-solving within Active Directory environment
- Proficiency in analyzing and interpreting telemetry logs
- Ability to generate and recognize attack signatures and patterns
- Enhanced knowledge of network protocols and security vulnerabilities 




### Tools Used

- Virtual machine systems (Windows, Windows Server, Linux) for creating the test environment
- Telemetry generation tools to create realistic attack scenarios
- Logging tools to check the history and logs of events that take place



## Steps

### <ins>Step 1 - Building logical diagram</ins>

We will begin by creating a logical diagram to map out the environment setup. The primary purpose of this diagram is to visualize and understand the data flow paths, allowing us to see how all the components are interconnected and how data moves through the system. Included below are the IP's used along with which system is connected to which for reference if following along. 



![diagram mini project 1](https://github.com/user-attachments/assets/38b7100f-e018-4751-a667-f8652d6785c4)



### <ins>Step 2 - Installing virtual machines</ins>


### Virtual Machine Setup for Brute Force Attack Simulation

#### Step 1: Install VirtualBox
1. **Download and Install VirtualBox**:
   - Ensure you have the latest version of VirtualBox installed on your Windows machine.

#### Step 2: Obtain ISO Files
1. **Required ISO Files**:
   - Windows 10
   - Windows Server (2022, 2019, or 2016)
   - Kali Linux
   - Ubuntu Server 22.04.x

#### Step 3: Configure Virtual Machines
1. **Create and Configure VMs**:
   - Open VirtualBox and create new virtual machines for each ISO file.
   - Follow the VirtualBox setup wizard for each VM, ensuring they meet the minimum system requirements for each operating system.

2. **Set Up Network Configuration**:
   - Ensure all virtual machines are on the same internal network.
   - Follow the logical diagram for IP address assignments.
   - Refer to the images below for correct DNS server configurations.

#### Step 4: Assign IP Addresses and DNS Servers
1. **Windows 10**:
   - Set IP address according to the logical diagram.
   - Configure the DNS server as shown in the provided images.

2. **Windows Server**:
   - Assign a static IP address.
   - Configure DNS settings to match the logical diagram.

3. **Kali Linux**:
   - Edit network settings to assign a static IP.
   - Set the DNS server as per the provided images.

4. **Ubuntu Server**:
   - Configure the network interface to use a static IP address.
   - Set the DNS server as per the logical diagram.

### Example Network Configuration

#### Windows Machine:
1. **Set Static IP and DNS**:
   - IP Address: Follow the logical diagram (e.g., 192.168.10.7).
   - Subnet Mask: 255.255.255.0
   - Default Gateway: 192.168.10.1
   - Preferred DNS Server: 8.8.8.8

2. **Steps**:
   - Go to `Network and Internet Settings`.
   - Click `Change adapter options`.
   - Right-click the network adapter and select `Properties`.
   - Select `Internet Protocol Version 4 (TCP/IPv4)` and click `Properties`.
   - Enter the IP address, subnet mask, default gateway, and preferred DNS server.
   - Click `OK` to save the settings.

#### Windows Server:
1. **Install and Configure Active Directory**:
   - Open `Server Manager`, then go to `Manage > Add Roles and Features`.
   - Select `Role-based or feature-based installation`.
   - Choose the server and select `Active Directory Domain Services`.
   - Click `Add Features`, then `Next`, and `Install`.
   - After installation, promote the server to a domain controller.
   - Create a new forest with a name like `example.test`.
   - Complete the wizard, adding a password and keeping other settings default.

2. **Create Organizational Units and Users**:
   - Open `Active Directory Users and Computers`.
   - Create organizational units (e.g., IT, HR).
   - Create test users in each organizational unit.

3. **Join Target Machine to Domain**:
   - Change DNS server to the IP address of the domain controller.
   - Go to `This PC > Properties > Advanced system settings > Computer Name`.
   - Change to the domain created (e.g., `example.test`).
   - Authenticate using a test user account.

#### Kali Linux:
1. **Network Configuration**:
   - Edit connections and set the IP address, netmask, and DNS server.
   - Disconnect and reconnect the network to apply changes.

2. **System Update and Tool Installation**:
   - Update and upgrade the system:
     ```bash
     sudo apt-get update && sudo apt-get upgrade
     ```
   - Create a project directory and install Crowbar:
     ```bash
     mkdir ad-project
     sudo apt-get install -y crowbar
     ```

3. **Prepare Wordlist**:
   - Unzip and prepare the rockyou.txt wordlist:
     ```bash
     cd /usr/share/wordlists
     sudo gunzip rockyou.txt.gz
     cp rockyou.txt ~/Desktop/ad-project/
     cd ~/Desktop/ad-project
     head -n 20 rockyou.txt > passwords.txt
     nano passwords.txt
     ```
   - Add a known password (e.g., `Words89`), then save and exit.

#### Ubuntu Server:
1. **Network Configuration**:
   - Configure the network interface for a static IP address.
   - Set the DNS server as specified in the logical diagram.

Following these steps ensures all virtual machines are correctly set up and networked, providing a solid foundation for conducting the brute force attack simulation.












### <ins>Step 3 - Installing splunk</ins> 

### Splunk Machine Configuration

#### Step 1: Check and Set IP Address
1. Open the Splunk machine and run:
   ```bash
   ip a
   ```
   - Verify the current IP address.

2. Edit the IP configuration to match the logical diagram:
   ```bash
   sudo nano /etc/netplan/00-installer-config.yaml
   ```

3. Update the configuration file:
   - Change `dhcp4` value from `true` to `no`.
   - Add the following lines:
     ```yaml
     addresses:
       - 192.168.10.10/24
     nameservers:
       addresses: [8.8.8.8]
     routes:
       - to: 0.0.0.0/0
         via: 192.168.10.1
     ```

4. Save and exit the editor.

5. Apply the changes:
   ```bash
   sudo netplan apply
   ```

6. Verify the changes:
   ```bash
   ip a
   ping google.com
   ```

#### Step 2: Install VirtualBox Guest Additions
1. Download Splunk on your host machine.

2. On the Splunk virtual machine, install the guest additions:
   ```bash
   sudo apt-get install virtualbox-guest-additions-iso
   sudo apt-get install virtualbox-guest-utils
   ```

3. Set up shared folders:
   - Go to Devices > Shared Folders > Shared Folder Settings.
   - Select the folder path where Splunk was downloaded.
   - Ensure all three checkboxes are selected (Auto-mount, Make Permanent, Read-only).
   - Leave the folder name to match the folder path.

4. Reboot the VM:
   ```bash
   sudo reboot
   ```

5. Add the user to the `vboxsf` group:
   ```bash
   sudo adduser <username> vboxsf
   ```

6. Create and verify the shared directory:
   ```bash
   mkdir ~/share
   ls -la ~/share
   ```

7. Mount the shared folder:
   ```bash
   sudo mount -t vboxsf -o uid=1000,gid=1000 <folder_name> ~/share/
   ```

#### Step 3: Install Splunk
1. Navigate to the shared directory:
   ```bash
   cd ~/share/
   ls -la
   ```

2. Install Splunk:
   ```bash
   sudo dpkg -i splunk.deb
   ```

3. Change to the Splunk directory:
   ```bash
   cd /opt/splunk
   sudo -u splunk bash
   ```

4. Start Splunk and set up an admin account:
   ```bash
   cd bin
   ./splunk start
   ```
   - Agree to the terms and create an admin user and password when prompted.

5. Enable Splunk to start at boot:
   ```bash
   exit
   cd /opt/splunk/bin
   sudo ./splunk enable boot-start -user splunk
   ```
   - This will ensure Splunk runs with the user `splunk` upon reboot.




Installing splunk universal forwarder and sysmon on target machine and server - step 4

### Windows Server Configuration

#### Step 1: Rename and Restart the Virtual Machine
1. Enter the Windows Server virtual machine.
2. Rename it to `ADDC01`.
3. Restart the server upon prompt.

#### Step 2: Configure IP Address
1. Open Command Prompt and check the current IP address using `ipconfig`.
2. Change the IP address to match the logical diagram.
3. Right-click the network icon and go to Network & Internet settings.
4. Click on "Change adapter options".
5. Right-click the main adapter and select Properties.
6. Double-click "Internet Protocol Version 4 (TCP/IPv4)".
7. Configure the following settings:
   - IP Address: `192.168.10.100`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: `192.168.10.1`
   - Preferred DNS Server: `8.8.8.8`

8. Verify changes with `ipconfig`.

#### Step 3: Test Connection to Splunk Server
1. Open a web browser and enter `192.168.10.10:8000` to test the connection.
   - Ensure the Splunk server is running.

### Splunk Installation

#### Step 4: Download and Install Splunk on Windows Server
1. Download the Windows Server version of Splunk.
2. Run the installer and proceed through the installation with default settings.
3. Configure the Receiving Indexer:
   - Enter Splunk Server IP: `192.168.10.10`
   - Default Port: `9997`

### Sysmon Installation

#### Step 5: Download and Install Sysmon
1. Download Sysmon from the Microsoft website.
2. Download the Sysmon Olaf Config from GitHub.
   - Navigate to the `sysmonconfig.xml` file, click the raw button, and save the file to the Downloads folder.
3. Extract the downloaded Sysmon zip folder.
4. Copy the file path of the extracted folder.
5. Open PowerShell as Administrator and navigate to the extracted folder directory:
   ```bash
   cd <extracted_folder_path>
   ```
6. Install Sysmon with the configuration file:
   ```bash
   .\Sysmon64.exe -i ..\sysmonconfig.xml
   ```

### Splunk Universal Forwarder Configuration

#### Step 6: Configure Splunk Universal Forwarder
1. Navigate to the following path:
   ```bash
   Program Files > SplunkUniversalForwarder > etc > system > local
   ```
2. Create a new `inputs.conf` file with the following content:
   ```ini
   [default]
   host = ADDC01

   [WinEventLog://Application]
   index = endpoint

   [WinEventLog://Security]
   index = endpoint

   [WinEventLog://System]
   index = endpoint

   [XmlWinEventLog://Microsoft-Windows-Sysmon/Operational]
   index = endpoint
   ```
3. Save the file as `inputs.conf`.

#### Step 7: Restart Splunk Universal Forwarder Service
1. Open Services as Administrator.
2. Locate `SplunkForwarder`.
3. Change the service account to `Local System Account`.
4. Apply changes and restart the service.

### Finalize Splunk Server Configuration

#### Step 8: Configure Splunk Server
1. Go to `192.168.10.10:8000` in your web browser and log in.
2. Navigate to Settings > Indexes.
3. Create a new index named `endpoint`.
4. Go to Settings > Forwarding and Receiving.
5. Configure Receiving:
   - Add new receiving port `9997`.

#### Step 9: Verify Data Ingestion
1. Navigate to Apps > Search & Reporting.
2. In the search bar, type `index=endpoint` and click the search icon.
3. Verify the incoming data from the host `ADDC01`.

### Repeat Configuration on Target PC
1. Follow the same steps to configure Sysmon and Splunk on the target PC.
2. Use the same `inputs.conf` file and respective IP addresses as per the logical diagram.




### <ins>Step 4 -Install and configure active directory on Windows server then promoting it to domain controller then make the target machine join the new domain</ins> 

### Windows Server Configuration

#### Step 1: Set Static IP Address
1. **Check IP Address**:
   - Open Command Prompt and run `ipconfig` to check the current IP address.

2. **Configure Static IP Address**:
   - Go to `Network and Internet Settings` > `Change Adapter Options`.
   - Right-click the network adapter and select `Properties`.
   - Double-click `Internet Protocol Version 4 (TCP/IPv4)`.
   - Set the following values:
     - IP Address: `192.168.10.7`
     - Subnet Mask: `255.255.255.0`
     - Default Gateway: `192.168.10.1`
     - Preferred DNS Server: `8.8.8.8`

#### Step 2: Install Active Directory Domain Services (AD DS)
1. **Open Server Manager**:
   - Go to `Manage` > `Add Roles and Features`.

2. **Installation Wizard**:
   - Click `Next`.
   - Select `Role-based or feature-based installation`.
   - Select the server to use.
   - Choose `Active Directory Domain Services` and click `Add Features`.
   - Click `Next`, `Next`, then `Install`.

3. **Promote Server to Domain Controller**:
   - After installation, click the flag icon at the top of Server Manager.
   - Select `Promote this server to a domain controller`.
   - Choose `Add a new forest` and enter a domain name (e.g., `example.test`).
   - On the next screen, leave default settings and add a password.
   - Continue through the wizard with default settings until installation begins.
   - The server will sign out upon completion.

#### Step 3: Create Users and Organizational Units
1. **Sign Back In**:
   - Open Server Manager.

2. **Create Organizational Units and Users**:
   - Go to `Tools` > `Active Directory Users and Computers`.
   - In the left sidebar, right-click the domain and select `New` > `Organizational Unit`.
   - Name the unit (e.g., `IT`).

3. **Create Test User**:
   - Right-click the `IT` organizational unit and select `New` > `User`.
   - Enter the user's details (e.g., `Henry Johnson`) and set a password.
   - Uncheck `User must change password at next logon` for testing purposes.

4. **Create Another Department**:
   - Repeat the steps to create another organizational unit (e.g., `HR`).

### Windows Target Machine Configuration

#### Step 4: Join Target Machine to the Domain
1. **Configure DNS**:
   - Go to `Network and Internet Settings` > `Change Adapter Options`.
   - Set the DNS server value to `192.168.10.7`.

2. **Join the Domain**:
   - Go to `This PC`, right-click and select `Properties`.
   - Click `Advanced system settings`.
   - Under the `Computer Name` tab, click `Change`.
   - Enter the domain name (e.g., `example.test`).
   - When prompted, enter the credentials of a domain user (e.g., `Henry Johnson`).

3. **Restart and Log In**:
   - Restart the target machine.
   - On the login screen, select `Other user`.
   - Ensure you are signing into the domain (e.g., `example\Henry Johnson`).








### <ins>Step 5 -performing a brute force attack using kali linux view the queries through splunk and setup and install atomic red team running atomic tests</ins> 


### Kali Linux Configuration for Brute Force Attack Simulation

#### Step 1: Network Configuration
1. **Login to Kali Linux**:
   - Default credentials: `username: kali`, `password: kali`.

2. **Edit Network Settings**:
   - Right-click the network icon at the top and select `Edit Connections`.
   - Under `IPv4 Settings`, set the IP address, netmask, and DNS server according to the diagram.
   - Click the network icon, then disconnect and reconnect to apply the changes.

#### Step 2: System Update
1. **Update and Upgrade Repositories**:
   - Open Terminal and run: 
     ```bash
     sudo apt-get update && sudo apt-get upgrade
     ```

#### Step 3: Directory and Tool Setup
1. **Create Project Directory**:
   - In Terminal, create a new directory: 
     ```bash
     mkdir ad-project
     ```

2. **Install Crowbar**:
   - Install Crowbar, a brute force tool:
     ```bash
     sudo apt-get install -y crowbar
     ```

#### Step 4: Wordlist Preparation
1. **Unzip RockYou Wordlist**:
   - Change to the wordlists directory and unzip rockyou.txt.gz:
     ```bash
     cd /usr/share/wordlists
     sudo gunzip rockyou.txt.gz
     ```

2. **Copy Wordlist to Project Directory**:
   - Copy the unzipped wordlist to the project directory:
     ```bash
     cp rockyou.txt ~/Desktop/ad-project/
     ```

3. **Create a Password Subset**:
   - Change to the project directory:
     ```bash
     cd ~/Desktop/ad-project
     ```
   - Create a smaller password list with the first 20 passwords:
     ```bash
     head -n 20 rockyou.txt > passwords.txt
     ```
   - Ensure the passwords are copied correctly:
     ```bash
     cat passwords.txt
     ```

4. **Add Known Password to List**:
   - Open `passwords.txt` for editing:
     ```bash
     nano passwords.txt
     ```
   - Add the known password (e.g., `Words89`), then save and exit (Ctrl+X).

#### Step 5: Enable Remote Desktop on Target Machine
1. **Configure Remote Desktop**:
   - On the target Windows machine:
     - Right-click `This PC` > `Properties` > `Advanced system settings`.
     - Select the `Remote` tab and check `Allow remote connections to this computer`.
     - Add users to the remote desktop users list.

#### Step 6: Perform Brute Force Attack
1. **Run Crowbar**:
   - Execute the following command in the `ad-project` directory:
     ```bash
     crowbar -b rdp -u kroger -C passwords.txt -s 192.168.10.100/32
     ```
   - This command specifies:
     - `-b rdp`: Use Remote Desktop Protocol.
     - `-u kroger`: The user to target.
     - `-C passwords.txt`: The password list to use.
     - `-s 192.168.10.100/32`: The target machine's IP address.

#### Step 7: Analyze Results in Splunk
1. **View Telemetry in Splunk**:
   - Log into Splunk and navigate to `Search and Reporting`.
   - Search for events related to the attack:
     ```splunk
     index=endpoint kroger
     ```
   - Filter results to the last 15 minutes.

2. **Identify Event Codes**:
   - Look for EventCode `4625` (failed logon) and `4624` (successful logon).
   - Analyze the patterns and timestamps to confirm the brute force attack and its impact.


