---
title: PowerShell - Como criar imagens VM com Packer
description: Saiba como usar o Packer e o PowerShell para criar imagens de máquinas virtuais em Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: a2cd6152f9c8e75223fcf1aab77fee7700223979
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561012"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>PowerShell: Como usar o Packer para criar imagens de máquinas virtuais em Azure
Cada máquina virtual (VM) em Azure é criada a partir de uma imagem que define a distribuição do Windows e a versão OS. As imagens podem incluir aplicações e configurações pré-instaladas. O Azure Marketplace fornece muitas imagens de primeira e de terceiros para ambientes de aplicação e SISTEMA mais comuns, ou pode criar as suas próprias imagens personalizadas adaptadas às suas necessidades. Este artigo detalha como usar a ferramenta de código aberto [Packer](https://www.packer.io/) para definir e construir imagens personalizadas em Azure.

Este artigo foi testado pela última vez em 8/5/2020 usando [a](https://www.packer.io/docs/install) versão Packer 1.6.1.

> [!NOTE]
> O Azure tem agora um serviço, Azure Image Builder (pré-visualização), para definir e criar as suas próprias imagens personalizadas. O Azure Image Builder é construído em Packer, para que possa até usar os scripts existentes do proserdor de conchas Packer com ele. Para começar com o Azure Image Builder, consulte [Criar um Windows VM com Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Criar grupo de recursos Azure
Durante o processo de construção, a Packer cria recursos temporários do Azure à medida que constrói a origem VM. Para capturar essa fonte VM para ser usada como imagem, deve definir um grupo de recursos. A saída do processo de construção packer é armazenada neste grupo de recursos.

Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myPackerGroup* na localização *este:*

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Criar credenciais do Azure
Packer autentica com Azure usando um principal de serviço. Um diretor de serviço Azure é uma identidade de segurança que pode usar com apps, serviços e ferramentas de automação como o Packer. Você controla e define as permissões sobre as operações que o diretor de serviço pode executar em Azure.

Crie um chefe de serviço com [o New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). O valor para `-DisplayName` as necessidades deve ser único; substitua-o pelo seu próprio valor, se necessário.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

Em seguida, des output a palavra-passe e o ID da aplicação.

```powershell
$plainPassword
$sp.ApplicationId
```


Para autenticar a Azure, também precisa de obter o seu inquilino Azure e iDs de subscrição com [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definir modelo de Packer
Para construir imagens, cria-se um modelo como ficheiro JSON. No modelo, você define construtores e provisionadores que realizam o processo de construção real. A Packer tem um [construtor para a Azure](https://www.packer.io/docs/builders/azure.html) que lhe permite definir os recursos Azure, como as credenciais principais de serviço criadas no passo anterior.

Crie um ficheiro nomeado *windows.js* e cole o seguinte conteúdo. Insira os seus próprios valores para os seguintes valores:

| Parâmetro                           | Onde obter |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Ver iD principal do serviço com `$sp.applicationId` |
| *client_secret*                     | Ver a palavra-passe gerada automaticamente com `$plainPassword` |
| *tenant_id*                         | Saída do `$sub.TenantId` comando |
| *subscription_id*                   | Saída do `$sub.SubscriptionId` comando |
| *managed_image_resource_group_name* | Nome do grupo de recursos que criou no primeiro passo |
| *managed_image_name*                | Nome para a imagem gerida do disco que é criada |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
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

    "build_resource_group_name": "myPackerGroup",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Este modelo constrói um VM windows Server 2016, instala o IIS e, em seguida, generaliza o VM com sysprep. A instalação do IIS mostra como pode utilizar o provisionador PowerShell para executar comandos adicionais. A imagem final do Packer inclui então a instalação e configuração do software necessários.

O Windows Guest Agent participa no processo Sysprep. O agente deve ser totalmente instalado antes que o VM possa ser sysprep'ed. Para garantir que isso é verdade, todos os serviços de agente devem estar a funcionar antes de executar sysprep.exe. O corte JSON anterior mostra uma maneira de fazê-lo no provisionador PowerShell. Este corte só é necessário se o VM estiver configurado para instalar o agente, que é o padrão.


## <a name="build-packer-image"></a>Construa a imagem do Packer
Se ainda não tiver o Packer instalado na sua máquina local, [siga as instruções de instalação do Packer](https://learn.hashicorp.com/packer/getting-started/install).

Construa a imagem abrindo um pedido de cmd e especificando o seu ficheiro de modelo Packer da seguinte forma:

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

Leva alguns minutos para packer construir o VM, executar os provisões, e limpar a implementação.


## <a name="create-a-vm-from-the-packer-image"></a>Criar um VM a partir da imagem Packer
Agora pode criar um VM a partir da sua imagem com [New-AzVM](/powershell/module/az.compute/new-azvm). Os recursos da rede de apoio são criados se já não existirem. Quando solicitado, introduza um nome de utilizador administrativo e uma palavra-passe a criar no VM. O exemplo a seguir cria um VM chamado *myVM* da *myPackerImage:*

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

Se desejar criar VMs num grupo ou região de recursos diferente do que a sua imagem Packer, especifique o ID de imagem em vez do nome da imagem. Pode obter o ID de imagem com [Get-AzImage](/powershell/module/az.compute/get-azimage).

Demora alguns minutos a criar o VM a partir da sua imagem Packer.


## <a name="test-vm-and-webserver"></a>Teste VM e webserver
Obtenha o endereço IP público do seu VM com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIP* criado anteriormente:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Para ver o seu VM, que inclui a instalação IIS do provisionador Packer, em ação, insira o endereço IP público num navegador web.

![Site predefinido do IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Passos seguintes
Também pode utilizar scripts de provisionadores Packer existentes com [O Azure Image Builder](image-builder.md).
