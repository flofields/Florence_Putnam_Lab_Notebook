---
LAYOUT: post
TITLE: transferring files from Andromeda to Unity through Globus Connect
CATEGORY: [ Protocol ]
TAG: [ Diploria labyrinthiformis, Montastrea cavernosa, Madracis decactis ]
---

## Transferring files from Andromeda to Unity through the Globus Connect platform
This pipeline is based on Zoe Dellaert's Instructions found on github [here](https://github.com/zdellaert/ZD_Putnam_Lab_Notebook/blob/master/_posts/2025-02-19-Globus-Andromeda-Unity.md) and information on the use of unity and guidelines on scratch directories, moving files in unity can be found [here](https://github.com/Putnam-Lab/Lab_Management/blob/master/Bioinformatics_&_Coding/Unity.md) 


### Objective
The main objective of this post is to use the developed pipeline to transfer files from [Andromeda](https://its.uri.edu/research-computing/using-andromeda/) (URI HPC server) to [Unity](https://unity.rc.umass.edu/shibboleth-ds/?entityID=https%3A%2F%2Funity.rc.umass.edu%2Fshibboleth&return=https%3A%2F%2Funity.rc.umass.edu%2FShibboleth.sso%2FLogin%3FSAMLDS%3D1%26target%3Dss%253Amem%253A9b5a703085c5a10c0badac6ab755e0a5399253d74d463123dc4f2c34f2af9a0c) using [Globus Connect Server](https://docs.globus.org/globus-connect-server/v5.4/) online database. 

<span style="color: red;">Note: The transfer of files can only be done on campus (URI) while signed into your andromeda account or off campus using the vpn.</span>

#### 1. First you need to sign into Andromeda and access Global connect. <span style="color: red;">This step to sign in only needs to be done once.

	interactive
	module load GlobusConnectPersonal/3.2.0
	globusconnectpersonal

You will recieve a message in the terminal stated below.  Note: in this example i will not be showing the URL provided, the auth code or the endpoint id for security purposes.

	Detected that setup has not run yet, and '-setup' was not used
	Will now attempt to run
	  globusconnectpersonal -setup

	Globus Connect Personal needs you to log in to continue the setup process.

	We will display a login URL. Copy it into any browser and log in to get a
	single-use code. Return to this command with the code to continue setup.
	
	Login here:
	-----
	URL GIVEN TO YOU WILL BE HERE
	-----

	Enter the auth code: CODE

	== starting endpoint setup


	Input a value for the Endpoint Name: ~/data/putnamlab/
	registered new endpoint, id: ENDPOINT
	setup completed successfully

	Will now start globusconnectpersonal in GUI mode
	Graphical environment not detected

	To launch Globus Connect Personal in CLI mode, use
	  globusconnectpersonal -start

	Or, if you want to force the use of the GUI, use
	  globusconnectpersonal -gui

#### 2. The URL should be copied and entered in your bowser. It should take you to the globus connect online server. Login with URI SSO credentials

#### 3. Enter the auth code into the terminal

#### 4. Enter "~/data/putnamlab/" as endpoint when prompted

#### 5. Record the endpoint string that is given here


#### 6. Edit your globus config file. This step makes the endpoint readable and writable. Then enter the path and save the file. Note your config file should be empty when  you open it.
 
	nano ~/.globusonline/lta/config-paths
	/data/putnamlab/,0,1

Now you should be all set up and can transfer files but following the steps below

#### 1. You need to first activate the endpoint from Andromeda

	interactive
	module load GlobusConnectPersonal/3.2.0
	globusconnectpersonal -start &

Go to [Globus](https://www.globus.org/) then sign in
Go to the File Manager and in the Collection field on the left, enter the personal endpoint string that was spit out by the globus setup above
Input the address below (exact address of Unity) in the collection field on the right

	acda5457-9c06-4564-8375-260ba428f22a 

Select the files or folders you want to transfer from Andromeda to Unity and press ‘Start’.