# Installation

- [Getting Started Tutorial](../index.md)
  - [Installation](index.md)
    - [Docker Installation](#docker-installation)
    - [Eclipse Installation](#eclipse-installation)
    - [Workspace Setup](#workspace-setup)
    - [Whats Next](#whats-next)
  - [Basic Communication](../communicationtutorial/index.md)
  - [User Management](../usermanagement/index.md)
  - [Simple Example Game](../tutorial/index.md)
- [Quick Reference](../quickreference/index.md)


MicroNet itself does not require any installation but it requires both a **Docker Engine** and an **Eclipse IDE** to be installed on the developers host. MicroNet was developed with Microsoft Windows but also works under Linux, and MacOS. The MicroNet Tools however are only tested on Windows. 

## Docker Installation

The most convenient way to use docker on a developer's computer is by installing the **Docker Desktop** version which is available for *MacOS* and *Microsoft Windows 10 Professional*. On Linux a native docker installation can be used.  

### Windows
On Windows a Windows Pro version is required to use Docker Desktop because it requires **HyperV** to be enabled which is not possible on Windows Home Editions (see requirements in [Install Docker for Windows](https://docs.docker.com/docker-for-windows/install/)). If you have Windows 10 Pro, enable **HyperV** as explained in the Windows documentation [Install Hyper-V on Windows 10](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Afterwards download and install the appropriate **Docker Desktop** client which can be found via the [Official Docker Installation Documentation](https://docs.docker.com/engine/installation/).

If you don't have Windows 10 Pro the best alternative solution is to use the [Docker Toolbox](#docker-toolbox) as explained [below](#docker-toolbox). In any case test your docker installation by using the `docker info` command.

### Linux

On Linux choose the appropriate installation guide for your Linux opperating system: 
[Ubuntu](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/), 
[Debian](https://docs.docker.com/engine/installation/linux/docker-ce/debian/), 
[CentOS](https://docs.docker.com/engine/installation/linux/docker-ce/centos/), or
[Fedora](https://docs.docker.com/engine/installation/linux/docker-ce/fedora/).

> MicroNet has not been tested on Linux Operating Systems 

### MacOS

On Mac follow the official Docker [Installation Manual for MacOS](https://docs.docker.com/docker-for-mac/install/).

> MicroNet has not been tested on MacOS

### Docker Toolbox

A fallback plan if none of the above solutions work is to use **Docker Toolbox**. The Docker Toolbox uses *VirtualBox* as its virtualization technology which is widely supported on older operating systems. Use the [Official Docker Toolbox Documentation](https://docs.docker.com/toolbox/overview/) to help you through the Docker Toolbox installation process.

## Eclipse Installation

An **Eclipse Oxygen** installation is required to use the MicroNet tools. It is recommended to download a new Eclipse Installation and use it solely for MicroNet because the MicroNet tools automatically synchronize specific files in the workspace. You do not want this behaviour for any other workspace other than MicroNet workspaces.

Download the *Eclipse IDE for Java Developers* from the [Eclipse Download Page](https://www.eclipse.org/downloads/eclipse-packages/) for your specific operating system as shown in the image below. Be sure to download the **packaged version** of Eclipse and not the installer to ensure the separation of the Eclipse used by MicroNet to other Eclipse installations. 

![download-eclipse](EclipseInstallation.png "Download Eclipse")

> The Enterprise Edition of Eclipse (Eclipse IDE for Java EE Developers) is not required for MicroNet development and it is therefore not recommended, to not overwhelm the developer with unused IDE funcionalities.

Extract the *eclipse* directory from the archive to you hard drive and start the contained eclipse executable. 

## MicroNet Tools Installation

Once Eclipse is open, navigate to the *Help -> Install New Software...* dialog. Press the *Add...* button in the top right to add a new Update Site to this Eclipse Installation. Enter *MicroNet* as the name and add *https://mrharibo.github.io/updatesite/* as the location. Choose the MicroNet update site from the "Work with:" drop-down and install the MicroNet plug-in. This plug-in contains all MicroNet Tools.

> Sometimes the MicroNet category is not shown and the check box *Group items by category* must be deselected for MicroNet to show up as indicated in the image below.

![micronet-update-site](UpdatesiteMarked.PNG "MicroNet Update Site")

### Install Docker Toolbox

Although not mandatory it is recommended to install the *Docker Tools for Eclipse*. The Docker Tools are a handy collection of visual tools that make the management of the Docker Engine more comfortable. The Eclipse Docker Tools can be installed via the Eclipse Marketplace under *Help -> Eclipse Marketplace...*. Just enter "Docker Tools" in the search field as shown in the image below.

![install-eclipse-docker-tools](EclipseDockerTools.PNG "Eclipse Docker Tools Installation")

## Workspace Setup

When you open Eclipse you will be prompted a *Workspace Directory*. Every Game developed with MicroNet needs it's own workspace directory referred to as the **Game Workspace**. You can use the same MicroNet Eclipse installation to develop multiple games by using a different workspace for each game. In this tutorial the Game Workspace directory will be "/MyGame".

### MicroNet Settings

The worspace of a game contains a set of metadata that is automatically generated when the workspace is opened for the first time. The first step after opening the workspace should be to configure the generated metadata according to the game you want to develop. Open the MicroNet global preference page under *Window -> Preferences -> MicroNet*.

![micronet-prefs](Settings.PNG "MicroNet Preference Page")

#### Docker Settings

Use the Docker Settings section to test if your Docker installation works correctly with MicroNet. If you are using Docker Desktop everything should work right away and you can verify that by pressing the *Test Docker* button. If you are using Docker Toolbox specify the Docker Toolbox installation directory and also test your Docker Installation. Check the *use Docker Toolbox* checkbox accordingly.

#### Application Docker Network

To ensure connectivity among Microservices a custom Docker Network must be created. It is recommended to use a separate network for all games that are developed in parallel to ensure game isolation. Enter a name for the network and press *Create Network*. Test your network with the *Test Network* button. You can also use a Docker Network that was created in advance but be sure to test it. In this tutorial "mn_bridge_network" will be used as the network name.

#### Application Workspace

The workspace of a game itself is a Maven project (containing a pom.xml) and the projects within the workspace are children of this parent Maven project and represent the (M/m)icroservices??? of the application. The parent pom.xml is used to build the complete game application in one swipe.

Edit the game metadata according to the specification of your game and apply the changes using the *Apply Application Pom Metadata* button to automatically store the metadata in the parent pom.xml. In this tutorial *MyGame* will be used as the groupId, *MyGameApp* will be used as the artifactId, and *0.0.1-SNAPSHOT* will be used as the version.

> Be advised that in the current version 0.1.0 of MicroNet, changes to the parent pom.xml are not updated in the pom.xml files in child projects (service projects). Momentarily it is best to define the game Metadata once and leave it unchanged. In the rare case a change is necessary the child pom's need to be updated by hand. Automation for this is planned for the future. 

### Opening the MicroNet Views

As a last step open all **MicroNet Views**. Open the *Window -> Show View -> Other* dialog and select all views listed under the *MicroNet* category. Also select the **Docker Explorer** under the *Docker* category. Confirm to open all views.

![#open-views](OpeningViews.PNG "Open the relevant views")

## Whats Next

If you have successfully complete the previous steps you completed the installation process and are ready to start developing your first MicroNet application. Continue with the [Basic Communication](../communicationtutorial/index.md) Tutorial.
