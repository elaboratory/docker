page_title: PowerShell DSC Usage
page_description: Using DSC to configure a new Docker host
page_keywords: powershell, dsc, installation, usage, docker, documentation

# Using PowerShell DSC

Windows PowerShell Desired State Configuration (DSC) is a configuration
management tool that extends the existing functionality of Windows PowerShell.
DSC uses a declarative syntax to define the state in which a target should be
configured. More information about PowerShell DSC can be found at
http://technet.microsoft.com/en-us/library/dn249912.aspx.

## Requirements

To use this guide you'll need a Windows host with PowerShell v4.0 or newer.

The included DSC configuration script also uses the official PPA so
only an Ubuntu target is supported. The Ubuntu target must already have the
required OMI Server and PowerShell DSC for Linux providers installed. More
information can be found at https://github.com/MSFTOSSMgmt/WPSDSCLinux. The
source repository listed below also includes PowerShell DSC for Linux
installation and init scripts along with more detailed installation information.

## Installation

The DSC configuration example source is available in the following repository:
https://github.com/anweiss/DockerClientDSC. It can be cloned with:

    $ git clone https://github.com/anweiss/DockerClientDSC.git

## Usage

The DSC configuration utilizes a set of shell scripts to determine whether or
not the specified Docker components are configured on the target node(s). The
source repository also includes a script (`RunDockerClientConfig.ps1`) that can
be used to establish the required CIM session(s) and execute the
`Set-DscConfiguration` cmdlet.

More detailed usage information can be found at
https://github.com/anweiss/DockerClientDSC.

### Run Configuration
The Docker installation configuration is equivalent to running:

```
apt-get install docker.io
ln -sf /usr/bin/docker.io /usr/local/bin/docker
sed -i '$acomplete -F _docker docker' /etc/bash_completion.d/docker.io
```

Ensure that your current working directory is set to the `DockerClientDSC`
source and load the DockerClient configuration into the current PowerShell
session

```powershell
. .\DockerClient.ps1
```

Generate the required DSC configuration .mof file for the targeted node

```powershell
DockerClient -Hostname "myhost"
```

A sample DSC configuration data file has also been included and can be modified
and used in conjunction with or in place of the `Hostname` parameter:

```powershell
DockerClient -ConfigurationData .\DockerConfigData.psd1
```

Start the configuration application process on the targeted node

```powershell
.\RunDockerClientConfig.ps1 -Hostname "myhost"
```

The `RunDockerClientConfig.ps1` script can also parse a DSC configuration data
file and execute configurations against multiple nodes as such:

```powershell
.\RunDockerClientConfig.ps1 -ConfigurationData .\DockerConfigData.psd1
```

### Images
Image configuration is equivalent to running: `docker pull [image]`.

Using the same Run Configuration steps defined above, execute `DockerClient`
with the `Image` parameter:

```powershell
DockerClient -Hostname "myhost" -Image node
```

The configuration process can be initiated as before:

```powershell
.\RunDockerClientConfig.ps1 -Hostname "myhost"
```

### Containers
Container configuration is equivalent to running:
`docker run -d --name="[containername]" [image] '[command]'`.

Using the same Run Configuration steps defined above, execute `DockerClient`
with the `Image`, `ContainerName`, and `Command` parameters:

```powershell
DockerClient -Hostname "myhost" -Image node -ContainerName "helloworld" `
-Command 'echo "Hello World!"'
```

The configuration process can be initiated as before:

```powershell
.\RunDockerClientConfig.ps1 -Hostname "myhost"
```
