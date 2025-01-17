# Defence In Depth: Part2
Full day session on Securing your Azure SQL Database


## Exercise 0 : Deploy a Baseline Azure SQL Database Environment

Before we can get started with the remainder of the exercises, we need a baseline deployment to start from. 

We will be making use of ARM Templates as well as PowerShell scripts throughout the exercises. The starting point combines some PowerShell scripting to configure the Azure Active Directory Users and Groups and then will deploy Azure resources using an ARM Template.


### Tasks  

**1.** Pull the repository from [GitHub](https://github.com/cairneym/DefenceInDepthPart2.git)  
**2.** Navigate to the ***00_BaselineDeploy\Solution*** folder  
**3.** Take a note of your Azure Domain Name.  If you created a Free Trial, then it will be in the format: ***\<yourName\>\<yourEmailDomain\>.onmicrosoft.com***. For example, if you signed up as ***ndc.user@outlook.com*** then your domain would be ***ndcuseroutlook.onmicrosoft.com***.  
**4.** From a PowerShell prompt, run ``.\Configure-ADGroups-And-Users.ps1``. Note the following:  
*   The script will prompt you for the ***Initial User Password*** that will be used for 3 new Azure Active Directory users.  This is a temporary password and you will be prompted to update it in a later exercise to make use of the users.       
*   The script will create 2 new regular users in your Azure Active Directory named ***user1@\<domain\>.onmicrosoft.com*** and ***user2@\<domain\>.onmicrosoft.com***. The ***Display Names*** will be ***NDC User1*** & ***NDC User2***.    
*   The script will create 1 new admin user in your Azure Active Directory named ***adminuser@\<domain\>.onmicrosoft.com***. The ***Display Name*** will be ***NDC Admin***.    
*   The script will create 1 new security group in your Azure Active Directory named ***SQL Admins***. The user ***NDC Admin*** and your own Azure Login will be added as members to this group.
*   The script also needs the ***Display Name*** for your sign-up account for your Azure Free Trial or personal subscription. This is to be able to retrieve your account to make it the owner of the Security Group and also to include it a member. This is the ***"FirstName LastName"*** that you entered at sign-up.  
*   The script will prompt you for a *uniquifier*. Since the SQL Server name and the VM DNS Name must be globally unique, this is used to attempt to ensure these are made unique.

**5.** If you need to deploy a development VM rather than just one for verification and testing, then edit the *VMDeployParams.json* template to change the value of the *vmSize* parameter from **Standard_B2s** to **Standard_B8ms**. Also update the *ScriptUrl* parameter to point to *installDevTools.ps1* instead of *installSSMS.ps1* (the remainder of the URI path stays the same).   
**6.** From a PowerShell prompt, run ``.\deployBaseline.ps1`` script, noting the following:  
*   The SQL Server ***"Server Name"*** needs to be globally unique, this is achived by the script appending the uniquifier to the name provided.  
*   The VM also needs to be a globally unique name. The same suffix as for the SQL Server will be appended to assure this.  
*   The SQL Server and VM should be named with lower case letters and numbers only.  
*   The resources are divided into 3 Resource Groups - one for the Networking, one for the SQL Server related resources and one for the VM. This is because the SQL Server and Network resources may be changed over time and the VM will stay pretty much static. Since the VM deployment is one of the longest tasks, we'll do it this way so we don't always wait on this resource when applying new ARM Templates.  
*   You will be prompted for the names of the Resource Groups. My suggestions are: *NDC-NET*, *NDC-DB* and *NDC-VM*.     
*   The VM administrator password must be between 12 and 123 characters in length. The login must be between 1 and 20 characters.  
*   You will be prompted for parameters for ***SQL Server Name***, ***SQL Admin Login***, ***SQL Admin Password***, ***VM Name***, ***VM Admin Login***, ***VM Admin Password***, ***5 character Uniquifier***.  

**7.** The VM will have Microsoft SQL Server Management Studio and the updated Microsoft Edge browser pre-installed. If you went for the Developer install, then additionally it will have Visual Studio 2019 Community Edition and Git pre-installed.    
**8.** If there are any issues with running this script, then you should clean up the new Group and Users in Azure Active Directory before re-running.  
**9.** If you went for the Developer INstall, login to the VM using RDP (See **Verification** steps below) and navigate to a suitable folder and run `git pull https://github.com/cairneym/DefenceInDepthPart2.git` to get the copy of the repository onto the VM.  


#### Verification

To verify your deployment, use SQL Server Management Studio or Azure Data Studio on your own computer and connect to the database.  Use the connection string ``<SQL Server Name>-<Uniquifier>.database.windows.net``.  Login using the *SQL Admin Login* and password and ensure that you also select *WideWorldImporters* for the database name.

Next, start an RDP session to your VM. This should be available through the DNS name ``<VM Name>-<uniquifier>.australiaeast.cloudapp.azure.com:3389``. Login using ***\<vmAdminLogin>*** as the login with the associated password. Once connected to there, start SQL Server Management Studio and connect to the *WideWorldImporters* database as above using the connection string ``<SQL Server Name>-<Uniquifier>.database.windows.net``.


