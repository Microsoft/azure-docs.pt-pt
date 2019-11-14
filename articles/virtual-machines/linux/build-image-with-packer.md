---
title: Como criar imagens de VM do Azure do Linux com o Packr
description: Saiba como usar o Packr para criar imagens de máquinas virtuais do Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: a9f0750908123c236596683ec2ad6de505c46213
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036952"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Como usar o Packer para criar imagens de máquina virtual do Linux no Azure
Cada VM (máquina virtual) no Azure é criada a partir de uma imagem que define a distribuição do Linux e a versão do sistema operacional. As imagens podem incluir aplicativos e configurações pré-instalados. O Azure Marketplace fornece muitas imagens primeiro e de terceiros para distribuições e ambientes de aplicativos mais comuns, ou você pode criar suas próprias imagens personalizadas adaptadas às suas necessidades. Este artigo fornece detalhes sobre como usar o [pacote](https://www.packer.io/) de ferramentas de código-fonte aberto para definir e criar imagens personalizadas no Azure.

> [!NOTE]
> Agora, o Azure tem um serviço, Construtor de imagens do Azure (visualização), para definir e criar suas próprias imagens personalizadas. O construtor de imagem do Azure é compilado no empacotador, portanto, você pode até mesmo usar seus scripts de provisionamento do shell do Pack. Para começar a usar o construtor de imagens do Azure, confira [criar uma VM do Linux com o construtor de imagens do Azure](image-builder.md).


## <a name="create-azure-resource-group"></a>Criar grupo de recursos do Azure
Durante o processo de compilação, o Packer cria recursos temporários do Azure à medida que cria a VM de origem. Para capturar essa VM de origem para uso como uma imagem, você deve definir um grupo de recursos. A saída do processo de Build do empacotador é armazenada nesse grupo de recursos.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Criar credenciais do Azure
O Pack é autenticado com o Azure usando uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o Packr. Você controla e define as permissões para quais operações a entidade de serviço pode executar no Azure.

Crie uma entidade de serviço com [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp) e gere as credenciais necessárias para o Pack:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Para autenticar no Azure, você também precisa obter sua ID de assinatura do Azure com [AZ Account show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Você usa a saída desses dois comandos na próxima etapa.


## <a name="define-packer-template"></a>Definir modelo de empacotador
Para criar imagens, você cria um modelo como um arquivo JSON. No modelo, você define construtores e provisionadores que executam o processo de compilação real. O Pack tem um [provisionador para o Azure](https://www.packer.io/docs/builders/azure.html) que permite que você defina recursos do Azure, como as credenciais da entidade de serviço criadas na etapa anterior.

Crie um arquivo chamado *Ubuntu. JSON* e cole o conteúdo a seguir. Insira seus próprios valores para o seguinte:

| Parâmetro                           | Onde obter |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Primeira linha de saída do `az ad sp` criar comando- *AppID* |
| *client_secret*                     | Segunda linha de saída do `az ad sp` criar comando- *senha* |
| *tenant_id*                         | Terceira linha de saída do `az ad sp` criar o *locatário* de comando |
| *subscription_id*                   | Saída do comando `az account show` |
| *managed_image_resource_group_name* | Nome do grupo de recursos que você criou na primeira etapa |
| *managed_image_name*                | Nome da imagem de disco gerenciado que é criada |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Este modelo cria uma imagem do Ubuntu 16, 4 LTS, instala o NGINX e, em seguida, desprovisiona a VM.

> [!NOTE]
> Se você expandir este modelo para provisionar credenciais de usuário, ajuste o comando de provisionamento que desprovisiona o agente do Azure para ler `-deprovision` em vez de `deprovision+user`.
> O sinalizador `+user` remove todas as contas de usuário da VM de origem.


## <a name="build-packer-image"></a>Imagem do empacotador de compilação
Se você ainda não tiver o Pack instalado em seu computador local, [siga as instruções de instalação do Pack](https://www.packer.io/docs/install/index.html).

Crie a imagem especificando o arquivo de modelo do empacotador da seguinte maneira:

```bash
./packer build ubuntu.json
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
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


## <a name="create-vm-from-azure-image"></a>Criar VM da imagem do Azure
Agora você pode criar uma VM com base em sua imagem com [AZ VM Create](/cli/azure/vm). Especifique a imagem que você criou com o parâmetro `--image`. O exemplo a seguir cria uma VM denominada *myVM* de *myPackerImage* e gera chaves SSH, se elas ainda não existirem:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Se você quiser criar VMs em um grupo de recursos ou região diferente da imagem do pacote, especifique a ID da imagem em vez do nome da imagem. Você pode obter a ID da imagem com [AZ Image Show](/cli/azure/image#az-image-show).

Leva alguns minutos para criar a VM. Depois que a VM tiver sido criada, anote as `publicIpAddress` exibidas pela CLI do Azure. Esse endereço é usado para acessar o site do NGINX por meio de um navegador da Web.

Para permitir que o tráfego da Web aceda à VM, abra a porta 80 a partir da Internet com [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testar VM e NGINX
Agora, pode abrir um browser e introduzir `http://publicIpAddress` na barra de endereço. Forneça o seu próprio endereço IP público a partir do processo de criação da VM. A página padrão do NGINX é exibida como no exemplo a seguir:

![Site predefinido do NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Passos seguintes
Você também pode usar scripts de provisionamento do Packer com o [Construtor de imagens do Azure](image-builder.md).
