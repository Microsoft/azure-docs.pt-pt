---
title: Como criar imagens de VM do Linux do Azure com o Packer | Documentos da Microsoft
description: Saiba como utilizar o Packer para criar imagens de máquinas virtuais do Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
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
ms.openlocfilehash: c0ec2616d8bdcf3cfd6d649f12e9bfceea33690a
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467752"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Como utilizar o Packer para criar imagens de máquinas virtuais do Linux no Azure
Cada máquina virtual (VM) no Azure é criada a partir de uma imagem que define a distribuição de Linux e versão do SO. Imagens podem incluir aplicações pré-instaladas e configurações. O Azure Marketplace proporciona muitas imagens que o primeiro e de terceiros para distribuições mais comuns e ambientes de aplicativos, ou pode criar suas próprias imagens personalizadas ajustadas às suas necessidades. Este artigo fornece detalhes sobre como utilizar a ferramenta de código-fonte aberto [Packer](https://www.packer.io/) para definir e criar imagens personalizadas no Azure.

> [!NOTE]
> O Azure tem agora um serviço, o construtor de imagens do Azure (pré-visualização), para definir e criar suas próprias imagens personalizadas. Construtor de imagens do Azure baseia-se a Packer, portanto, pode até usar seus scripts de provisioner de shell Packer existentes com o mesmo. Para começar a utilizar com o construtor de imagens do Azure, veja [criar uma VM do Linux com o construtor de imagens do Azure](image-builder.md).


## <a name="create-azure-resource-group"></a>Criar grupo de recursos do Azure
Durante o processo de compilação, o Packer cria temporários recursos do Azure à medida que cria a VM de origem. Para capturar essa VM de origem para utilização como uma imagem, deve definir um grupo de recursos. A saída do processo de compilação do Packer é armazenada neste grupo de recursos.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Criar credenciais do Azure
Packer autentica com o Azure com um principal de serviço. Um principal de serviço do Azure é uma identidade de segurança que pode utilizar com aplicações, serviços e ferramentas de automatização, como o Packer. Pode controlar e define as permissões em relação às quais operações o principal de serviço pode executar no Azure.

Criar um serviço principal com [az ad sp create-for-rbac](/cli/azure/ad/sp) e as credenciais que precisa de Packer de saída:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Segue-se um exemplo da saída nos comandos anteriores:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Para autenticar para o Azure, também tem de obter o seu ID de subscrição do Azure com [show de conta de az](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Utilize a saída destes dois comandos no próximo passo.


## <a name="define-packer-template"></a>Definir o modelo do Packer
A criação de imagens, crie um modelo como um ficheiro JSON. O modelo, vai definir construtores e provisioners que executam o processo de compilação real. Packer tem um [provisioner para o Azure](https://www.packer.io/docs/builders/azure.html) que permite-lhe definir recursos do Azure, como o passo das credenciais de principal de serviço criadas no anterior.

Crie um ficheiro denominado *ubuntu.json* e cole o seguinte conteúdo. Introduza os seus próprios valores para o seguinte:

| Parâmetro                           | Onde obtê |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Primeira linha da saída de `az ad sp` criar comando - *appId* |
| *client_secret*                     | A segunda linha de saída a partir `az ad sp` criar comando - *palavra-passe* |
| *tenant_id*                         | Terceira linha da saída a partir `az ad sp` criar comando - *inquilino* |
| *subscription_id*                   | Saída de `az account show` comando |
| *managed_image_resource_group_name* | Nome do grupo de recursos que criou no primeiro passo |
| *managed_image_name*                | Nome para a imagem de disco gerido, que é criada |


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

Este modelo cria uma imagem de Ubuntu 16.04 LTS, instala o NGINX e então desprovisiona a VM.

> [!NOTE]
> Se expandir este modelo para aprovisionar as credenciais de utilizador, ajuste o comando de provisioner desprovisiona o agente do Azure para ler `-deprovision` vez `deprovision+user`.
> O `+user` sinalizador remove todas as contas de utilizador da VM de origem.


## <a name="build-packer-image"></a>Criar a imagem do Packer
Se ainda não tiver instalado no seu computador local, do Packer [siga as instruções de instalação do Packer](https://www.packer.io/docs/install/index.html).

Crie a imagem, especificando o Packer ficheiro de modelo da seguinte forma:

```bash
./packer build ubuntu.json
```

Segue-se um exemplo da saída nos comandos anteriores:

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

Demora alguns minutos para que Packer criar a VM, execute os provisioners e limpar a implementação.


## <a name="create-vm-from-azure-image"></a>Criar VM a partir da imagem do Azure
Agora pode criar uma VM a partir da sua imagem com [az vm criar](/cli/azure/vm). Especifique a imagem criada com o `--image` parâmetro. O exemplo seguinte cria uma VM com o nome *myVM* partir *myPackerImage* e gera chaves SSH caso estas ainda não existam:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Se pretender criar VMs num grupo de recursos diferente ou uma região que a imagem do Packer, especifique o ID de imagem em vez de um nome de imagem. Pode obter o ID de imagem com [show de imagem de az](/cli/azure/image#az-image-show).

Demora alguns minutos para criar a VM. Quando a VM tiver sido criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. Este endereço é utilizado para aceder ao site NGINX através de um navegador da web.

Para permitir que o tráfego da Web aceda à VM, abra a porta 80 a partir da Internet com [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testar a VM e NGINX
Agora, pode abrir um browser e introduzir `http://publicIpAddress` na barra de endereço. Forneça o seu próprio endereço IP público a partir do processo de criação da VM. A página NGINX predefinida é apresentada como no exemplo seguinte:

![Site predefinido do NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Passos Seguintes
Também pode utilizar scripts de provisioner Packer existentes com [construtor de imagens do Azure](image-builder.md).
