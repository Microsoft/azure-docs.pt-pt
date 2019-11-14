---
title: Como criar imagens de VM do Windows com o Packer no Azure
description: Saiba como usar o Packr para criar imagens de máquinas virtuais do Windows no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: b2ff9869b0de7a0285644bea462101cd1dc80b99
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039227"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Como usar o Packer para criar imagens de máquina virtual do Windows no Azure
Cada VM (máquina virtual) no Azure é criada a partir de uma imagem que define a distribuição do Windows e a versão do sistema operacional. As imagens podem incluir aplicativos e configurações pré-instalados. O Azure Marketplace fornece muitas imagens primeiro e de terceiros para OS ambientes de sistema operacional e de aplicativos mais comuns, ou você pode criar suas próprias imagens personalizadas adaptadas às suas necessidades. Este artigo fornece detalhes sobre como usar o [pacote](https://www.packer.io/) de ferramentas de código-fonte aberto para definir e criar imagens personalizadas no Azure.

Este artigo foi testado pela última vez em 2/21/2019 usando o [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) versão 1.3.0 e o [Packer](https://www.packer.io/docs/install/index.html) versão 1.3.4.

> [!NOTE]
> Agora, o Azure tem um serviço, Construtor de imagens do Azure (visualização), para definir e criar suas próprias imagens personalizadas. O construtor de imagem do Azure é compilado no empacotador, portanto, você pode até mesmo usar seus scripts de provisionamento do shell do Pack. Para começar a usar o construtor de imagens do Azure, confira [criar uma VM do Windows com o construtor de imagens do Azure](image-builder.md).

## <a name="create-azure-resource-group"></a>Criar grupo de recursos do Azure
Durante o processo de compilação, o Packer cria recursos temporários do Azure à medida que cria a VM de origem. Para capturar essa VM de origem para uso como uma imagem, você deve definir um grupo de recursos. A saída do processo de Build do empacotador é armazenada nesse grupo de recursos.

Crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Criar credenciais do Azure
O Pack é autenticado com o Azure usando uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o Packr. Você controla e define as permissões para quais operações a entidade de serviço pode executar no Azure.

Crie uma entidade de serviço com [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) e atribua permissões para a entidade de serviço para criar e gerenciar recursos com [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). O valor de `-DisplayName` precisa ser exclusivo; Substitua pelo seu próprio valor, conforme necessário.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Em seguida, gere a senha e a ID do aplicativo.

```powershell
$plainPassword
$sp.ApplicationId
```


Para autenticar no Azure, você também precisa obter suas IDs de locatário e assinatura do Azure com [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definir modelo de empacotador
Para criar imagens, você cria um modelo como um arquivo JSON. No modelo, você define construtores e provisionadores que executam o processo de compilação real. O Pack tem um [construtor para o Azure](https://www.packer.io/docs/builders/azure.html) que permite que você defina os recursos do Azure, como as credenciais da entidade de serviço criadas na etapa anterior.

Crie um arquivo chamado *Windows. JSON* e cole o conteúdo a seguir. Insira seus próprios valores para o seguinte:

| Parâmetro                           | Onde obter |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Exibir a ID da entidade de serviço com `$sp.applicationId` |
| *client_secret*                     | Exibir a senha gerada automaticamente com `$plainPassword` |
| *tenant_id*                         | Saída do comando `$sub.TenantId` |
| *subscription_id*                   | Saída do comando `$sub.SubscriptionId` |
| *managed_image_resource_group_name* | Nome do grupo de recursos que você criou na primeira etapa |
| *managed_image_name*                | Nome da imagem de disco gerenciado que é criada |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Esse modelo cria uma VM do Windows Server 2016, instala o IIS e generaliza a VM com o Sysprep. A instalação do IIS mostra como você pode usar o provisionamento do PowerShell para executar comandos adicionais. A imagem final do empacotador inclui a instalação e a configuração de software necessárias.


## <a name="build-packer-image"></a>Imagem do empacotador de compilação
Se você ainda não tiver o Pack instalado em seu computador local, [siga as instruções de instalação do Pack](https://www.packer.io/docs/install/index.html).

Crie a imagem abrindo um prompt de comando e especificando o arquivo de modelo do empacotador da seguinte maneira:

```
./packer build windows.json
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Leva alguns minutos para que o Pack crie a VM, execute os provisionadores e limpe a implantação.


## <a name="create-a-vm-from-the-packer-image"></a>Criar uma VM com base na imagem do empacotador
Agora você pode criar uma VM com base em sua imagem com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Os recursos de rede de suporte serão criados se ainda não existirem. Quando solicitado, insira um nome de usuário administrativo e uma senha a ser criada na VM. O exemplo a seguir cria uma VM chamada *myVM* de *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Se você quiser criar VMs em um grupo de recursos ou região diferente da imagem do pacote, especifique a ID da imagem em vez do nome da imagem. Você pode obter a ID da imagem com [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

Leva alguns minutos para criar a VM com base na imagem do empacotador.


## <a name="test-vm-and-webserver"></a>Testar VM e WebServer
Obtenha o endereço IP público de sua VM com [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIP* criado anteriormente:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Para ver sua VM, que inclui a instalação do IIS do provisionamento do Packer, em ação, insira o endereço IP público em um navegador da Web.

![Site predefinido do IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Passos seguintes
Você também pode usar scripts de provisionamento do Packer com o [Construtor de imagens do Azure](image-builder.md).
