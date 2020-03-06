---
title: Azure Cloud Shell Quickstart - PowerShell
description: Saiba como utilizar o PowerShell no seu navegador com a Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394316"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Quickstart para PowerShell em Azure Cloud Shell

Este documento detalha como utilizar a PowerShell em Cloud Shell no [portal Azure](https://portal.azure.com/).

> [!NOTE]
> Também está disponível uma [Bash in Azure Cloud Shell](quickstart.md) Quickstart.

## <a name="start-cloud-shell"></a>Iniciar a nuvem shell

1. Clique no botão **Cloud Shell** a partir da barra de navegação superior do portal Azure

   ![](media/quickstart-powershell/shell-icon.png)

2. Selecione o ambiente PowerShell a partir da queda e estará na unidade Azure `(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Executar comandos PowerShell

Executar comandos regulares da PowerShell na Cloud Shell, tais como:

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

## <a name="navigate-azure-resources"></a>Navegar recursos azure

 1. Enumere todas as suas subscrições a partir de `Azure` unidade

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` à sua subscrição preferida

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Veja todos os seus recursos Azure sob a subscrição atual

    Escreva `dir` para listar várias visualizações dos seus recursos Azure.

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

### <a name="allresources-view"></a>Visão allResources

Escreva `dir` sob `AllResources` diretório para ver os seus recursos Azure.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Explore grupos de recursos

 Você pode ir ao diretório `ResourceGroups` e dentro de um grupo de recursos específico pode encontrar máquinas virtuais.

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
> Pode notar que, da segunda vez que escreve `dir`, a Cloud Shell é capaz de exibir os itens muito mais rapidamente.
> Isto porque os itens infantis estão em cache na memória para uma melhor experiência do utilizador.
No entanto, pode sempre usá`dir -Force` para obter dados frescos.

### <a name="navigate-storage-resources"></a>Navegar recursos de armazenamento

Ao entrar no diretório `StorageAccounts`, você pode facilmente navegar todos os seus recursos de armazenamento

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Com a corda de ligação, pode utilizar o seguinte comando para montar a partilha de Ficheiros Azure.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Para mais detalhes, consulte [o Monte um Azure Files e aceda à partilha no Windows][azmount].

Também pode navegar nos diretórios sob a partilha de Ficheiros Azure da seguinte forma:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interaja com máquinas virtuais

Pode encontrar todas as suas máquinas virtuais sob a subscrição atual através de `VirtualMachines` diretório.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Invocar o script PowerShell através de VMs remotos

 > [!WARNING]
 > Consulte a [gestão remota de Problemas de VMs Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Assumindo que tem um VM, MyVM1, vamos usar `Invoke-AzVMCommand` para invocar um bloco de scriptPowerShell na máquina remota.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Também pode navegar para o diretório VirtualMachines primeiro e executá`Invoke-AzVMCommand` seguinte.

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Inicie sessão interativamente num VM remoto

Pode utilizar `Enter-AzVM` para iniciar sessão interativamente num VM em funcionamento em Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Você também pode navegar para o diretório `VirtualMachines` primeiro e executar `Enter-AzVM` da seguinte forma

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Descubra WebApps

Ao entrar no diretório `WebApps`, pode facilmente navegar nos recursos das suas aplicações web

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

Para autenticar servidores ou VMs usando SSH, gere o par de chaves público-privado na Cloud Shell e publique a chave pública para `authorized_keys` na máquina remota, como `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Pode criar chaves privadas e privadas SSH usando `ssh-keygen` e publicá-las para `$env:USERPROFILE\.ssh` na Cloud Shell.

### <a name="using-ssh"></a>Usando SSH

Siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) para criar uma nova configuração VM utilizando cmdlets Azure PowerShell.
Antes de chamar para `New-AzVM` para iniciar a implementação, adicione a chave pública SSH à configuração VM.
O VM recém-criado conterá a chave pública no local `~\.ssh\authorized_keys`, permitindo assim uma sessão SSH sem credenciais para o VM.

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

Sob `Azure` unidade, escreva `Get-AzCommand` para obter comandos Azure específicos do contexto.

Em alternativa, pode sempre utilizar `Get-Command *az* -Module Az.*` para descobrir os comandos Azure disponíveis.

## <a name="install-custom-modules"></a>Instalar módulos personalizados

Pode executá`Install-Module` para instalar módulos a partir da [Galeria PowerShell.][gallery]

## <a name="get-help"></a>Get-Help

Digite `Get-Help` para obter informações sobre powerShell em Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

Para um comando específico, ainda pode fazer `Get-Help` seguido de um cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Utilize ficheiros Azure para armazenar os seus dados

Pode criar um guião, digamos `helloworld.ps1`, e guardá-lo para o seu `clouddrive` usá-lo em sessões de conchas.

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

Da próxima vez que utilizar o PowerShell na Cloud Shell, o ficheiro `helloworld.ps1` existirá sob o diretório `$HOME\clouddrive` que monta a sua partilha de Ficheiros Azure.

## <a name="use-custom-profile"></a>Use o perfil personalizado

Pode personalizar o seu ambiente PowerShell, criando perfis PowerShell (s) - `profile.ps1` (ou `Microsoft.PowerShell_profile.ps1`).
Guarde-o sob `$profile.CurrentUserAllHosts` (ou `$profile.CurrentUserAllHosts`), para que possa ser carregado em todas as sessões da PowerShell na Cloud Shell.

Para como criar um perfil, consulte [sobre perfis][profile].

## <a name="use-git"></a>Use Git

Para clonar um repo Git na Cloud Shell, é necessário criar um [token][githubtoken] de acesso pessoal e usá-lo como nome de utilizador. Assim que tiver o seu símbolo, clone o repositório da seguinte forma:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Sair da shell

Escreva `exit` terminar a sessão.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
