# Getting Started

This page explains which steps are required to setup MicroNet.

* [Installation](#installation)
  * [Docker Installation](#docker-installation)
  * [Eclipse Installation](#eclipse-installation)
* [Workspace Setup](#workspace-setup)
* [Whats Next](#whats-next)

## Installation

MicroNet itself does not require any installation but it requires both a **Docker Engine** and an **Eclipse IDE** installation on the developers host. MicroNet was developed with Microsoft Windows but is to work also in Linux, and MacOS. The MicroNet Tools however where only tested in Windows. 

### Docker Installation

The most convenient way to install docker on a developer's computer is by using the **Docker Desktop** version which is available for *MacOS* and *Microsoft Windows 10*. On Linux a native docker installation can be used.  

#### Windows
On Windows a Windows Pro version is required to use Docker Desktop because it requires **HyperV** to be enabled which is not possible on Windows Home Editions (see requirements in [Install Docker for Windows](https://docs.docker.com/docker-for-windows/install/)). If you have Windows 10 Pro, enable **HyperV** as explained in the Windows Documentation [Install Hyper-V on Windows 10](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Afterwards download and install the appropriate **Docker Desktop** client which can be found on the [Official Docker Installation Documentation](https://docs.docker.com/engine/installation/).

If you don't have Windows 10 Pro the best alternative solution is to use the [Docker Toolbox](#docker-toolbox) as explained [below](#docker-toolbox). In any case test your docker installation using the for example the `docker info` command.

#### Linux

On Linux choose the appropriate installation guide for your Linux opperating system: 
[Ubuntu](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/), 
[Debian](https://docs.docker.com/engine/installation/linux/docker-ce/debian/), 
[CentOS](https://docs.docker.com/engine/installation/linux/docker-ce/centos/), or
[Fedora](https://docs.docker.com/engine/installation/linux/docker-ce/fedora/).

> MicroNet has not been tested on Linux Operating Systems 

#### MacOS

On Mac follow the official Docker [Installation Manual for MacOS](https://docs.docker.com/docker-for-mac/install/).

> MicroNet has not been tested on MacOS

#### Docker Toolbox

A fallback plan if none of the above solutions works is to use the **Docker Toolbox**. The Docker Toolbox uses *VirtualBox* as the virtualization technology which widely supportet. Docker Toolbox is also available on older operating systems. Use the [Official Documentation](https://docs.docker.com/toolbox/overview/) to learn how to install Docker Toolbox.

### Eclipse Installation

An **Eclipse Oxygen** installation is required to use the MicroNet tools. It is recommended to download a new Eclipse Installation and use it solely for MicroNet because the MicroNet tools automatically synchronize specific files in the workspace which you do not want for projects other than MicroNet.

Download the *Eclipse IDE for Java Developers* from the [Eclipse Download Page](https://www.eclipse.org/downloads/eclipse-packages/) which is appropriate for your operating system. Be shure to download the packaged version of Eclipse and not the installer to enshure the separation of the Eclipse used by MicroNet to other Eclipse Installations. 

> The Enterprise Edition of Eclipse (Eclipse IDE for Java EE Developers) is not required for MicroNet development and it is therefore not recommended to not overwhelm the developer with unused IDE funcionalities.   

## Workspace Setup

When you open Eclipse you will be prompted for a *Workspace Directory*. Every Game developed with MicroNet needs it's own Workspace Directory. You can use the same MicroNet Eclipse installation for multiple games by using different workspaces for each game. In this tutorial a directory called "MyGame" will be used as Workspace Directory.

The worspace of a game contains a set of metadata wich is automatically generated when the workspace is opened for the first time. The first step after opening the workspace should be to configure the generated metadata according to the game you want to develop. Open the MicroNet global preference page under *Window -> Preferences -> MicroNet*.

## Whats Next
