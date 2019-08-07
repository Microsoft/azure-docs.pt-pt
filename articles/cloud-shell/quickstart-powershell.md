---
title: Guia de início rápido do Azure Cloud Shell | Microsoft Docs
description: Início rápido para Azure Cloud Shell
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: damaerte
ms.openlocfilehash: 36683d04b6f087f1d326458a07b043a0932191f1
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742009"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Início rápido do PowerShell no Azure Cloud Shell

Este documento fornece detalhes sobre como usar o PowerShell em Cloud Shell no [portal do Azure](https://portal.azure.com/).

> [!NOTE]
> Um [bash no guia de](quickstart.md) início rápido Azure cloud Shell também está disponível.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="start-cloud-shell"></a>Iniciar Cloud Shell

1. Clique no botão **Cloud Shell** na barra de navegação superior da portal do Azure

   ![](media/quickstart-powershell/shell-icon.png)

2. Selecione o ambiente do PowerShell na lista suspensa e você estará na unidade do Azure`(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Executar comandos do PowerShell

Execute comandos regulares do PowerShell na Cloud Shell, como:

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

## <a name="navigate-azure-resources"></a>Navegar pelos recursos do Azure

 1. Listar todas as suas assinaturas da `Azure` unidade

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd`para sua assinatura preferida

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Exibir todos os recursos do Azure na assinatura atual

    Digite `dir` para listar várias exibições de seus recursos do Azure.

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>Exibição de recursos

Digite `dir` em`AllResources` diretório para exibir os recursos do Azure.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Explorar grupos de recursos

 Você pode ir para o `ResourceGroups` diretório e dentro de um grupo de recursos específico que pode encontrar máquinas virtuais.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Você pode observar que, na segunda vez que digita `dir`, a Cloud Shell é capaz de exibir os itens muito mais rapidamente.
> Isso ocorre porque os itens filho são armazenados em cache na memória para uma melhor experiência do usuário.
No entanto, você sempre `dir -Force` pode usar o para obter dados atualizados.

### <a name="navigate-storage-resources"></a>Navegar pelos recursos de armazenamento

Ao entrar `StorageAccounts` no diretório, você pode navegar facilmente por todos os seus recursos de armazenamento

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Com a cadeia de conexão, você pode usar o comando a seguir para montar o compartilhamento de arquivos do Azure.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Para obter detalhes, consulte [montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows][azmount].

Você também pode navegar pelos diretórios no compartilhamento de arquivos do Azure da seguinte maneira:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interagir com máquinas virtuais

Você pode encontrar todas as suas máquinas virtuais na assinatura atual por `VirtualMachines` meio do diretório.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Invocar o script do PowerShell entre VMs remotas

 > [!WARNING]
 > Veja [solução de problemas de gerenciamento remoto de VMs do Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Supondo que você tenha uma VM, MyVM1, vamos `Invoke-AzVMCommand` usar para invocar um bloco de script do PowerShell no computador remoto.

  ```azurepowershell-interactive
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```

  Você também pode navegar até o diretório VirtualMachines primeiro e executar `Invoke-AzVMCommand` da seguinte maneira.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo}

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Fazer logon interativamente em uma VM remota

Você pode usar `Enter-AzVM` o para fazer logon interativamente em uma VM em execução no Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Você também pode navegar até o `VirtualMachines` diretório primeiro e executar `Enter-AzVM` da seguinte maneira

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM
 ```

### <a name="discover-webapps"></a>Descobrir webapps

Ao entrar `WebApps` no diretório, você pode navegar facilmente pelos recursos de aplicativos Web

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

Para autenticar servidores ou VMS usando o ssh, gere o par de chaves pública-privada em Cloud Shell e publique a chave `authorized_keys` pública no computador remoto, `/home/user/.ssh/authorized_keys`como.

> [!NOTE]
> Você pode criar chaves públicas privadas SSH usando `ssh-keygen` e publicá `$env:USERPROFILE\.ssh` -las no no Cloud Shell.

### <a name="using-ssh"></a>Usando SSH

Siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) para criar uma nova configuração de VM usando cmdlets Azure PowerShell.
Antes de chamar `New-AzVM` para iniciar a implantação, adicione a chave pública SSH à configuração da VM.
A VM recém-criada conterá a chave pública no `~\.ssh\authorized_keys` local, habilitando assim a sessão SSH sem credencial para a VM.

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

## <a name="list-available-commands"></a>Listar comandos disponíveis

Em `Azure` unidade, digite `Get-AzCommand` para obter comandos do Azure específicos do contexto.

Como alternativa, você sempre pode usar `Get-Command *az* -Module Az.*` o para descobrir os comandos disponíveis do Azure.

## <a name="install-custom-modules"></a>Instalar módulos personalizados

Você pode executar `Install-Module` o para instalar os módulos do [Galeria do PowerShell][gallery].

## <a name="get-help"></a>Obter ajuda

Digite `Get-Help` para obter informações sobre o PowerShell no Azure cloud Shell.

```azurepowershell-interactive
Get-Help
```

Para um comando específico, você ainda `Get-Help` pode seguir por um cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Use os arquivos do Azure para armazenar seus dados

Você pode criar um script, digamos `helloworld.ps1`, e salvá-lo em `clouddrive` seu para usá-lo em sessões de Shell.

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

Na próxima vez que você usar o PowerShell no Cloud Shell `helloworld.ps1` , o arquivo existirá `$HOME\clouddrive` no diretório que monta o compartilhamento de arquivos do Azure.

## <a name="use-custom-profile"></a>Usar perfil personalizado

Você pode personalizar o ambiente do PowerShell criando perfil (s) do PowerShell- `profile.ps1` (ou `Microsoft.PowerShell_profile.ps1`).
Salve-o `$profile.CurrentUserAllHosts` em ( `$profile.CurrentUserAllHosts`ou), para que ele possa ser carregado em cada PowerShell na sessão Cloud Shell.

Para saber como criar um perfil, consulte [about][profile]Profiles.

## <a name="use-git"></a>Usar git

Para clonar um repositório git no Cloud Shell, você precisa criar um [token de acesso pessoal][githubtoken] e usá-lo como o nome de usuário. Depois de ter seu token, clone o repositório da seguinte maneira:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Sair da shell

Digite `exit` para encerrar a sessão.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
