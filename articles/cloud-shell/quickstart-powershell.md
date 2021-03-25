---
title: Azure Cloud Shell Quickstart - PowerShell
description: Aprenda a usar o PowerShell no seu navegador com a Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 7ff58c4e463b4ad47680b9140403e9ae5e22b057
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045285"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Quickstart para PowerShell em Azure Cloud Shell

Este documento detalha como utilizar o PowerShell em Cloud Shell no [portal Azure](https://portal.azure.com/).

> [!NOTE]
> Um [Bash in Azure Cloud Shell](quickstart.md) Quickstart também está disponível.

## <a name="start-cloud-shell"></a>Iniciar Cloud Shell

1. Clique no botão **Cloud Shell** a partir da barra de navegação superior do portal Azure

   ![Screenshot mostrando como iniciar Azure Cloud Shell a partir do portal Azure.](media/quickstart-powershell/shell-icon.png)

2. Selecione o ambiente PowerShell a partir do drop-down e estará na unidade Azure `(Azure:)`

   ![Screenshot mostrando como selecionar o ambiente PowerShell para a Azure Cloud Shell.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Executar comandos PowerShell

Executar comandos PowerShell regulares na Cloud Shell, tais como:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### <a name="interact-with-virtual-machines"></a>Interaja com máquinas virtuais

Pode encontrar todas as suas máquinas virtuais sob a subscrição atual através do `VirtualMachines` diretório.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Invocar script PowerShell através de VMs remotos

 > [!WARNING]
 > Consulte a [gestão remota de Azure VMs](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Assumindo que tem um VM, MyVM1, vamos usar para invocar um bloco de `Invoke-AzVMCommand` scripts PowerShell na máquina remota.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Você também pode navegar para o diretório VirtualMachines primeiro e executar `Invoke-AzVMCommand` o seguinte.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Faça login interativamente num VM remoto

Pode utilizar `Enter-AzVM` para iniciar sessão interativamente num VM em execução em Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Você também pode navegar para o `VirtualMachines` diretório primeiro e executar `Enter-AzVM` o seguinte

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Descubra WebApps

Ao entrar no `WebApps` diretório, pode facilmente navegar nos recursos das suas aplicações web

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Para autenticar para servidores ou VMs usando SSH, gere o par de chaves público-privado em Cloud Shell e publique a chave pública para `authorized_keys` a máquina remota, como `/home/user/.ssh/authorized_keys` .

> [!NOTE]
> Pode criar chaves públicas privadas SSH usando `ssh-keygen` e publicá-las `$env:USERPROFILE\.ssh` na Cloud Shell.

### <a name="using-ssh"></a>Utilização de SSH

Siga as instruções [aqui](../virtual-machines/linux/quick-create-powershell.md) para criar uma nova configuração VM utilizando cmdlets Azure PowerShell.
Antes de ligar `New-AzVM` para iniciar a implementação, adicione a chave pública SSH à configuração VM.
O VM recém-criado conterá a chave pública no `~\.ssh\authorized_keys` local, permitindo assim a sessão de SSH sem credenciais para o VM.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Lista de comandos disponíveis

Em `Azure` unidade, `Get-AzCommand` escreva para obter comandos Azure específicos de contexto.

Em alternativa, pode sempre utilizar `Get-Command *az* -Module Az.*` para descobrir os comandos Azure disponíveis.

## <a name="install-custom-modules"></a>Instalar módulos personalizados

Pode correr `Install-Module` para instalar módulos a partir da [PowerShell Gallery][gallery].

## <a name="get-help"></a>Get-Help

Escreva `Get-Help` para obter informações sobre powerShell em Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

Para um comando específico, ainda pode fazer `Get-Help` o que se segue a um cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Utilize ficheiros Azure para armazenar os seus dados

Você pode criar um script, `helloworld.ps1` digamos, e guardá-lo para você `clouddrive` usá-lo em sessões de shell.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Da próxima vez que utilizar o PowerShell na Cloud Shell, o `helloworld.ps1` ficheiro existirá sob o `$HOME\clouddrive` diretório que monta a sua partilha de Ficheiros Azure.

## <a name="use-custom-profile"></a>Use o perfil personalizado

Pode personalizar o seu ambiente PowerShell, criando o perfil PowerShell - `profile.ps1` (ou `Microsoft.PowerShell_profile.ps1` .
Guarde-o em `$profile.CurrentUserAllHosts` `$profile.CurrentUserAllHosts` (ou), para que possa ser carregado em todas as sessões PowerShell em Cloud Shell.

Para criar um perfil, consulte [sobre perfis.][profile]

## <a name="use-git"></a>Use Git

Para clonar um git repo na Cloud Shell, você precisa criar um [token de acesso pessoal][githubtoken] e usá-lo como nome de utilizador. Assim que tiver o seu símbolo, clone o repositório da seguinte forma:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Sair da shell

Escreva `exit` para terminar a sessão.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
