---
title: PowerShell no guia de introdução do Azure Cloud Shell | Documentos da Microsoft
description: Início rápido para o PowerShell no Cloud Shell
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
ms.openlocfilehash: 4e15953252ef03554b1eb666a8d05645b66d9bf2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805244"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Início rápido para o PowerShell no Azure Cloud Shell

Este documento fornece detalhes sobre como utilizar o PowerShell no Cloud Shell no [portal do Azure](https://portal.azure.com/).

> [!NOTE]
> R [no Azure Cloud Shell de Bash](quickstart.md) início rápido também está disponível.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="start-cloud-shell"></a>Iniciar Cloud Shell

1. Clique em **Cloud Shell** botão da barra de navegação superior do portal do Azure

  ![](media/quickstart-powershell/shell-icon.png)

2. Selecione o ambiente do PowerShell na lista suspensa e estará na unidade do Azure `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Execute comandos do PowerShell

Execute comandos do PowerShell regulares no Cloud Shell, tais como:

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

## <a name="navigate-azure-resources"></a>Navegue por recursos do Azure

 1. Listar todas as suas subscrições do `Azure` unidade

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` a sua subscrição preferencial

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Ver todos os seus recursos do Azure sob a subscrição atual

    Tipo de `dir` para listar várias vistas de recursos do Azure.

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

### <a name="allresources-view"></a>Vista de AllResources

Tipo `dir` em `AllResources` diretório para ver os recursos do Azure.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Explore os grupos de recursos

 Pode ir para o `ResourceGroups` diretório e dentro de um grupo de recursos específico pode encontrar máquinas virtuais.

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
> Poderá reparar que na segunda vez quando escrever `dir`, o Cloud Shell é capaz de exibir os itens muito mais rápidos.
> Isso ocorre porque os itens subordinados são colocadas em cache na memória para uma melhor experiência de utilizador.
No entanto, pode sempre utilizar `dir -Force` para obter dados atualizados.

### <a name="navigate-storage-resources"></a>Navegue por recursos do armazenamento

Ao introduzir para o `StorageAccounts` diretório, pode navegar facilmente todos os seus recursos de armazenamento

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Com a cadeia de ligação, pode utilizar o comando seguinte para montar a partilha de ficheiros do Azure.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Para obter detalhes, consulte [montar uma partilha de ficheiros do Azure e aceder à partilha no Windows][azmount].

Também pode navegar os diretórios abaixo da partilha de ficheiros do Azure da seguinte forma:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interagir com as máquinas virtuais

Pode encontrar todas as suas máquinas virtuais na subscrição atual por meio de `VirtualMachines` diretório.

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
 > Consulte a [resolução de problemas de gestão remota de VMs do Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Pressupondo que tenha uma VM, MyVM1, vamos utilizar `Invoke-AzVMCommand` para invocar um bloco de script do PowerShell na máquina remota.

  ```azurepowershell-interactive
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```

  Também pode navegar para o diretório de VirtualMachines pela primeira vez e executar `Invoke-AzVMCommand` da seguinte forma.

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>Iniciar sessão interativamente numa VM remota

Pode usar `Enter-AzVM` para iniciar sessão interativamente numa VM em execução no Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Também pode navegar para o `VirtualMachines` directory primeiro e execução `Enter-AzVM` da seguinte forma

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM
 ```

### <a name="discover-webapps"></a>Detetar WebApps

Ao introduzir para o `WebApps` diretório, pode navegar facilmente os seus recursos de aplicações web

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

Para autenticar servidores ou VMs através de SSH, gerar o par de chaves públicas-privadas no Cloud Shell e publicar a chave pública para `authorized_keys` na máquina remota, como `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Pode criar chaves de pública e privada SSH com `ssh-keygen` e publique-os para `$env:USERPROFILE\.ssh` no Cloud Shell.

### <a name="using-ssh"></a>Através de SSH

Siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) para criar uma nova configuração de VM com cmdlets do AzureRM.
Chamando antes `New-AzVM` para arrancar a implementação, adicione a chave pública SSH à configuração da VM.
A VM acabada de criar irá conter a chave pública no `~\.ssh\authorized_keys` localização, ativando assim livre de credencial sessão SSH à VM.

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

Sob `Azure` unidade, escreva `Get-AzCommand` para obter os comandos do Azure específicas do contexto.

Em alternativa, pode sempre utilizar `Get-Command *azurerm* -Module AzureRM.*` para descobrir os comandos do Azure disponíveis.

## <a name="install-custom-modules"></a>Instalar módulos personalizados

Pode executar `Install-Module` para instalar os módulos do [galeria do PowerShell][gallery].

## <a name="get-help"></a>Get-Help

Tipo de `Get-Help` para obter informações sobre o PowerShell no Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

Para um comando específico, ainda pode fazer `Get-Help` seguido de um cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Utilizar ficheiros do Azure para armazenar os dados

Pode criar um script, digamos `helloworld.ps1`e guardá-lo para seu `clouddrive` usá-lo em todas as sessões de shell.

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

Próxima vez, quando utilizar o PowerShell no Cloud Shell, o `helloworld.ps1` ficheiros existem sob a `$HOME\clouddrive` diretório que monta a partilha de ficheiros do Azure.

## <a name="use-custom-profile"></a>Utilizar o perfil personalizado

Pode personalizar o ambiente do PowerShell, através da criação de PowerShell perfis - `profile.ps1` (ou `Microsoft.PowerShell_profile.ps1`).
Guarde-o para baixo `$profile.CurrentUserAllHosts` (ou `$profile.CurrentUserAllHosts`), para que possa ser carregado no cada PowerShell numa sessão do Cloud Shell.

Para saber como criar um perfil, consulte [sobre perfis][profile].

## <a name="use-git"></a>Utilize o Git

Para clonar um repositório de Git no Cloud Shell, tem de criar uma [token de acesso pessoal] [ githubtoken] e utilizá-lo como o nome de utilizador. Assim que tiver seu token, clone o repositório da seguinte forma:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Sair da shell

Tipo de `exit` para terminar a sessão.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
