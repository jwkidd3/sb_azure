Setting up your environment for the labs
========================================
In this lab you will find the prerequisites and steps to help you set up your computer. After completing the lab you will have a working environment, ready for the other labs.

Configure your computer
-----------------------

### Install Visual Studio Ultimate 2015 Preview
To run these Introduction to Microsoft Azure labs, **Visual Studio 2015 Preview** is required. It can be downloaded and installed from [here](http://www.visualstudio.com/en-us/downloads/visual-studio-2015-downloads-vs.aspx).

Alternatively, you can use Visual Studio 2013, provided the following have been installed:

* Visual Studio 2013 Update 3 (or higher) ([download](http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs)).

* Azure SDK for Visual Studio 2013 ([download](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409)).

	> **Note 1:** Depending on how many of the SDK dependencies you already have on your machine, installing the SDK could take a long time, from several minutes to a half hour or more.

    > **Note 2:** While not required to complete this lab, the [Web Essentials](http://vswebessentials.com/download) extension for Visual Studio improves the HTML and JavaScript editing experience by adding in features like missing Angular attribute detection and improved JavaScript IntelliSense.

	> **Note 3:** Visual Studio is only required if you will be executing the [Get started with Azure Websites and ASP.NET](../get-started-with-websites-and-asp-net), [Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js using Azure Active Directory to log in users](../build-single-page-app-with-webapi-and-angular) or [Build a web application with ASP.NET MVC using DocumentDB](../build-web-app-using-documentdb) labs.

### Install Azure SDK for .Net ###
The Microsoft Azure SDK for .Net is available through the [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx). For the purposes of this lab you need version 2.3 or higher.

To do so, follow these instructions:

1. Open Microsoft Web Platform Installer.
1. Find the row for **Microsoft Azure SDK for .Net** corresponding to the version of Visual Studio you have installed.  Click the **Add** button. 
1. Click **Install**.

![Install Microsoft Azure SDK for .Net](images/install-microsoft-azure-sdk-for-net.png?raw=true)

_Install Microsoft Azure SDK for .Net for your version of Visual Studio_

> **Note:** Azure SDK for .Net is only required if you will be executing the [Get started with Azure Websites and ASP.NET](../get-started-with-websites-and-asp-net), [Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js using Azure Active Directory to log in users](../build-single-page-app-with-webapi-and-angular) or [Build a web application with ASP.NET MVC using DocumentDB](../build-web-app-using-documentdb) labs.

####Install Windows PowerShell
Windows 8 comes with PowerShell 3.0 installed, but that may not be the case in other (earlier) versions of Windows. You can check whether PowerShell is installed and if the version will work for the purposes of this lab by following these steps:

1. From the **Start** screen, begin typing **power**. 

	This returns a scoped list of apps that includes **Windows PowerShell**. If Windows PowerShell does not appear in the search results, you can skip the next steps and proceed to installing it using the link provided below.

1. To open the console, click **Windows PowerShell**.

1. Execute the following command:

	```PowerShell
	$PSVersionTable.PSVersion
	```

	The execution of this command should return 3.0 or higher. 

	![Verifying Powershell version](images/verifying-powershell-version.png?raw=true)

	_Verifying the Windows PowerShell version_

If **Windows PowerShell** is not installed on your machine or you have an older version, download a newer version from the [Windows PowerShell Scripting](https://technet.microsoft.com/en-us/scriptcenter/dd742419.aspx) page.

>**Note:** Windows PowerShell is only required for the [Create Virtual Machine using PowerShell](../create-virtual-machine#creating-a-vm-using-powershell) task of the [Infrastructure As A Service in Microsoft Azure](../create-virtual-machine) lab.

### Install Microsoft Azure PowerShell Cmdlets

The **Azure PowerShell** modules and all dependencies are available through the [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx). To install it, follow these instructions:

1. Open Microsoft Web Platform Installer.
1. Find the row for **Microsoft Azure PowerShell with Microsoft Azure SDK** and click the **Add** button. 
1. Click **Install**.

![Install MS Azure Powershell in MS WebPlatform Installer](images/install-ms-azure-powershell.png?raw=true)

_Install Microsoft Azure PowerShell_

>**Note:** The Microsoft Azure PowerShell Cmdlets are only required for the _Create Virtual Machine using PowerShell_ task of the [Infrastructure As A Service in Microsoft Azure](../create-virtual-machine) lab.

###Install the Azure Cross-Platform Command-Line Interface

There are two ways to install the **Azure Cross-Platform Command-Line Interface** (or xplat-cli): using installer packages for Windows and OS X, or if Node.js is installed on your system, the **npm** command. 

Once the xplat-cli has been installed, you will be able to use the **azure** command from your command-line interface (Bash, Terminal, Command prompt) to access the xplat-cli commands.

In order to install the **Azure Cross-Platform Command-Line Interface** using the installer, just download the package for your platform and follow the instructions. The following installer packages are available:

* [Windows installer](http://go.microsoft.com/?linkid=9828653&clcid=0x409)

* [OS X installer](http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409)

	> **Note:** If Node.js is already installed on your system, use the following command to install the xplat-cli:

	> ```
	> npm install azure-cli -g
	> ```

	>**Note:** The Azure Cross-Platform Command-Line Interface is only required for the _Create Virtual Machine using Cross-Platform Command Line Interface_ task of the [Infrastructure As A Service in Microsoft Azure](../create-virtual-machine) lab.

To ensure that you have the **Azure Cross-Platform Command-Line Interface** correctly installed, open a **Command Prompt** and execute the following command:

	```
	azure
	```

	You should get a message similar to the following:

	```
	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Microsoft Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.13
	help:
	help:    Display help for a given command
	help:      help [options] [command]
	help:
	help:    Log in to an Azure subscription using Active Directory. Currently, the user can login only via Microsoft organizational account
	help:      login [options]
	help:
	help:    Log out from Azure subscription using Active Directory. Currently, the user can log out only via Microsoft organizational account
	help:      logout [options] [username]
	help:
	help:    Open the portal in a browser
	help:      portal [options]
	help:
	help:    Commands:
	help:      account        Commands to manage your account information and publish settings
	help:      config         Commands to manage your local settings
	help:      hdinsight      Commands to manage your HDInsight accounts
	help:      mobile         Commands to manage your Mobile Services
	help:      network        Commands to manage your Networks
	help:      sb             Commands to manage your Service Bus configuration
	help:      service        Commands to manage your Cloud Services
	help:      site           Commands to manage your Web Sites
	help:      sql            Commands to manage your SQL Server accounts
	help:      storage        Commands to manage your Storage objects
	help:      vm             Commands to manage your Virtual Machines
	help:
	help:    Options:
	help:      -h, --help     output usage information
	help:      -v, --version  output the application version
	```

### Clone or download content of this GitHub repository (optional but recommended)

The labs provided have a combination of text documentation and sample code. In order to have all documentation and all necessary sample files locally on your computer, we strongly recommend you to clone (using [Git](http://git-scm.com/)) or [download](https://github.com/Azure-Readiness/HOL-Intro-to-Azure/archive/master.zip) all content in this repository locally on your computer. If you download the zip archive, you will need to [unblock](http://blogs.msdn.com/b/delay/p/unblockingdownloadedfile.aspx) the zip file before extracting it.

Summary
-------

You have Visual Studio 2015 Preview, or Visual Studio 2013 with Update 3 (or higher) and the _Azure SDK for Visual Studio 2013_, installed on your computer.
