------------------------------------------------------------------------
# About
Since Symantec Endpoint Protection (SEP) shipped, there has been a fix tool ([RepairClonedImage.exe](http://www.symantec.com/docs/TECH163349)) available to fix duplicate hardware IDs. (Duplicate hardware IDs occur when the SEP client is improperly prepared for cloning.)

This project makes it simple to run the fix tool across multiple endpoints by leveraging the built-in Host Integrity feature of SEP.

------------------------------------------------------------------------
# Requirements
- SEP version 12.1.5 and higher
- Microsoft Windows (32-bit or 64-bit)
- If you have configured a password to stop SEP services, it must be disabled. (See steps below.)

------------------------------------------------------------------------
# How to use this tool

## Step 1: Create a special group and assign the policy
- Download the DAT file by clicking the **Download ZIP** button
- Import the HI policy into your Symantec Endpoint Protection Manager (SEPM)
    + Login to the **SEPM**
    + Click **Policies**
    + Click **Host Integrity**
    + Click **Import a Host Integrity Policy**
- Create the group **My Company\Repair Duplicate HWID** If you have configured a password to stop SEP services, it must be disabled using the following steps.
    + Login to the **SEPM**
    + Click **Clients** > **Policies** > **Password Settings**
    + Uncheck **Require a password to stop the client service**
    + Click **OK**

## Step 2: Copy RepairClonedImage.exe to <SEPM>\inetpub\content
- Pick one SEPM in your site to host the RepairClonedImage.exe tool and write down its hostname
    + This SEPM will be the webserver from which clients will download the fix tool. For this reason, you should pick a SEPM in your site with which all clients can successfully communicate.
- Download **RepairClonedImage.zip** from [TECH163349](http://www.symantec.com/docs/TECH163349)
- Extract the ZIP file
- Copy **RepairClonedImage.exe** (note the extension!) to **<SEPM>\inetpub\content**
    + Default location: **C:\Program Files (x86)\Symantec\Symantec Endpoint Protection Manager\inetpub\content**

## Step 3: Update the policy with your SEPM's hostname
- Open the Host Integrity policy named **Repair duplicate HWID**
    + Login to the **SEPM**
    + Click **Policies**
    + Click **Host Integrity**
    + Right-click the policy named  **Repair Duplicate HWID**
    + Click **Edit**
- Click **Requirements**
- Click **Repair duplicate HWID on SEP client** > **Edit**
- Click **File: Download a file**
- Replace **YOUR_SEPM_HOSTNAME_HERE** with the hostname of the SEPM you picked in step 2
    + If you wish, you can alternatively enter the IP address of your SEPM.
- Click **OK**
- Click **OK**

## Step 4: Move affected clients into this group
- To reset the the hardware ID of affected clients, move them into the group **My Company\Repair Duplicate HWID**. The clients will download and run the RepairClonedImage.exe tool shortly after their next heartbeat.
- Shortly after this tool is run, clients will check into the SEPM and should show up correctly.

**WARNING:** Do NOT move all clients into this group. Only move clients affected by this issue! Moving a large number of non-affected clients into this group will result in a large number of stale client records. These will be removed, by default, after 30 days, but there will be a large number of stale client records until then!

------------------------------------------------------------------------
# Tips
- This tool only fixes a problem. It does not resolve the cause of the problem. In order to prevent this from occurring again, you will need to properly [prepare the SEP clients for cloning before you clone it](http://www.symantec.com/docs/HOWTO54706).