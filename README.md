# AWS-Multi-Region-VPC-Peering-Project
This project demonstrates how to set up VPC Peering between two VPCs located in different AWS regions: Virginia (us-east-1) and Ohio (us-east-2). It also verifies connectivity between two EC2 instances hosted in each VPC.

---

## 📁 Project Structure

- **Region 1 (Virginia)**
  - VPC Name: `App-VPC`
  - CIDR: `10.0.0.0/16`
  - EC2 Instance: `server-1`

- **Region 2 (Ohio)**
  - VPC Name: `DB-VPC`
  - CIDR: `192.168.0.0/16`
  - EC2 Instance: `server-2`

---

## 🛠️ Step-by-Step Setup Guide

### 🔹 Step 1: Create VPC in Virginia (`us-east-1`)

1. **VPC Settings**
   - Name: `App-VPC`
   - CIDR: `10.0.0.0/16`
   - Enable DNS Hostnames and Resolution

2. **Create Subnet**
   - Name: `public-subnet-va`
   - CIDR: `10.0.1.0/24`
   - AZ: `us-east-1a`
   - Auto-assign public IP: **Enabled**

3. **Internet Gateway**
   - Create and attach to `App-VPC`

4. **Route Table**
   - Add route `0.0.0.0/0` → Internet Gateway

5. **Security Group**
   - Name: `SG-App`
   - Inbound: Allow **All traffic** from `0.0.0.0/0` *(for testing)*

6. **Launch EC2 Instance**
   - Name: `server-1`
   - AMI: Amazon Linux 2
   - Type: t2.micro
   - Key Pair: Your key
   - Subnet: `public-subnet-va`
   - SG: `SG-App`

---

### 🔹 Step 2: Create VPC in Ohio (`us-east-2`)

1. **VPC Settings**
   - Name: `DB-VPC`
   - CIDR: `192.168.0.0/16`
   - Enable DNS Hostnames and Resolution

2. **Create Subnet**
   - Name: `public-subnet-oh`
   - CIDR: `192.168.1.0/24`
   - AZ: `us-east-2a`
   - Auto-assign public IP: **Enabled**

3. **Internet Gateway**
   - Create and attach to `DB-VPC`

4. **Route Table**
   - Add route `0.0.0.0/0` → Internet Gateway

5. **Security Group**
   - Name: `SG-DB`
   - Inbound: Allow **All traffic** from `0.0.0.0/0` *(for testing)*

6. **Launch EC2 Instance**
   - Name: `server-2`
   - AMI: Amazon Linux 2
   - Type: t2.micro
   - Key Pair: Your key
   - Subnet: `public-subnet-oh`
   - SG: `SG-DB`

---

### 🔹 Step 3: Create VPC Peering (Cross-Region)

#### 🟢 From Virginia (Initiator)

1. Go to `VPC > Peering Connections`
2. Create peering:
   - Name: `peer-va-oh`
   - Requester: `App-VPC` (`us-east-1`)
   - Accepter Region: `us-east-2`
   - Accepter VPC ID: `DB-VPC` ID
3. Create Peering

#### 🔵 In Ohio (Accepter)

1. Switch to `us-east-2`
2. Go to `VPC > Peering Connections`
3. Select the request and click **Accept**

---

### 🔹 Step 4: Update Route Tables

#### 🛣️ In Virginia

- Edit `App-VPC` route table
- Add route:
  - Destination: `192.168.0.0/16`
  - Target: Peering Connection

#### 🛣️ In Ohio

- Edit `DB-VPC` route table
- Add route:
  - Destination: `10.0.0.0/16`
  - Target: Peering Connection

---

### 🔹 Step 5: Test Connectivity

1. SSH into `server-1` (Virginia)
2. Ping the **private IP** of `server-2` (Ohio)
3. Repeat vice versa from `server-2`
4. Success = VPC peering works!

---

