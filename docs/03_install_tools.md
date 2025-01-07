
### **INSTALLING THE COMMAND-LINE MANAGEMENT TOOLS**

The primary method used to manage Kubernetes is [kubectl][link09], the 
Kubernetes command-line tool. You can use kubectl to manage a cluster, 
deploy applications and view the logs.
 - [Install Kubectl][link10]


[Helm][link11] is a command-line tool used to manage software packages for Kubernetes.
It's the easiest, and most common way, to install applications to a Kubernetes
cluster. 
 - [Install Helm][link12]


Although you can create and manage AKS via the Azure Portal, managing 
AKS through PowerShell and/or the Azure Command-Line Interface (CLI) is much more 
efficient.
 - [Install PowerShell][link01]
 - [Install the Azure PowerShell Modules][link02]
 - [Install the Azure Command-Line Interface][link03]


[Visual Studio Code][link04] is an Integrated Development Environment (IDE)) 
that allows you to edit and run scripts. It's free, supports many languages,
supports [GIT][link06], and works on any OS. It's easier than copying and 
pasting code into the PowerShell/Azure CLI.
 - [Install Visual Studio Code][link05]

To install Visual Studio Code on Windows the latest .NET Framework runtime 
also needs to be installed.
 - [Install the .NET Framework 4.8][link07]

Once Visual Studio Code is installed, install the PowerShell extension.
 - [Install the Visual Studio Code PowerShell Extension][link08]

Microsoft also offers a Kubernetes extension for Visual Studio Code, which 
you can find and install from the extensions tab.


\
Next: [Creating an AKS Cluster](.\04_create_cluster.html) \
Previous: [Estimating Cluster Costs](.\02_costs.html) \
[Table of Contents](.\index.html)


[link01]: https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell
[link02]: https://learn.microsoft.com/en-us/powershell/azure/install-azure-powershell
[link03]: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli
[link04]: https://code.visualstudio.com
[link05]: https://code.visualstudio.com/docs/setup/setup-overview
[link06]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
[link07]: https://dotnet.microsoft.com/en-us/download/dotnet-framework/net48
[link08]: https://code.visualstudio.com/docs/languages/powershell
[link09]: https://kubernetes.io/docs/reference/kubectl/
[link10]: https://kubernetes.io/docs/tasks/tools/#kubectl
[link11]: https://helm.sh
[link12]: https://helm.sh/docs/intro/install/

<style>
    p + ul { margin-block-start: -15px }
    p      { margin-block-start:  20px }
</style>
