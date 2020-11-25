---
title: Criar Imagens VM Linux Azure com Packer
description: Saiba como usar o Packer para criar imagens de máquinas virtuais Linux em Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: 29a0c47bf24ecd916fb9402ffcb2a3ff13a36a84
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016425"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Como usar o Packer para criar imagens de máquinas virtuais Linux em Azure
Cada máquina virtual (VM) em Azure é criada a partir de uma imagem que define a distribuição Linux e a versão OS. As imagens podem incluir aplicações e configurações pré-instaladas. O Azure Marketplace fornece muitas imagens de primeira e de terceiros para a distribuição mais comum e ambientes de aplicações, ou pode criar as suas próprias imagens personalizadas adaptadas às suas necessidades. Este artigo detalha como usar a ferramenta de código aberto [Packer](https://www.packer.io/) para definir e construir imagens personalizadas em Azure.

> [!NOTE]
> O Azure tem agora um serviço, Azure Image Builder (pré-visualização), para definir e criar as suas próprias imagens personalizadas. O Azure Image Builder é construído em Packer, para que possa até usar os scripts existentes do proserdor de conchas Packer com ele. Para começar com o Azure Image Builder, consulte [Criar um Linux VM com Azure Image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Criar grupo de recursos Azure
Durante o processo de construção, a Packer cria recursos temporários do Azure à medida que constrói a origem VM. Para capturar essa fonte VM para ser usada como imagem, deve definir um grupo de recursos. A saída do processo de construção packer é armazenada neste grupo de recursos.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Criar credenciais do Azure
Packer autentica com Azure usando um principal de serviço. Um diretor de serviço Azure é uma identidade de segurança que pode usar com apps, serviços e ferramentas de automação como o Packer. Você controla e define as permissões sobre as operações que o diretor de serviço pode executar em Azure.

Crie um principal de serviço com [a ad sp criar para rbac](/cli/azure/ad/sp) e produzir as credenciais de que packer precisa:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Para autenticar a Azure, também precisa de obter o seu ID de subscrição Azure com [um show de conta Az:](/cli/azure/account)

```azurecli
az account show --query "{ subscription_id: id }"
```

Usas a saída destes dois comandos no próximo passo.


## <a name="define-packer-template"></a>Definir modelo de Packer
Para construir imagens, cria-se um modelo como ficheiro JSON. No modelo, você define construtores e provisionadores que realizam o processo de construção real. A Packer tem um [provisionador para o Azure](https://www.packer.io/docs/builders/azure.html) que lhe permite definir os recursos Azure, como as credenciais principais de serviço criadas no passo anterior.

Crie um ficheiro nomeado *ubuntu.js* e cole o seguinte conteúdo. Insira os seus próprios valores para os seguintes valores:

| Parâmetro                           | Onde obter |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Primeira linha de saída a partir de `az ad sp` criar comando - *appId* |
| *client_secret*                     | Segunda linha de saída a partir de `az ad sp` criar comando - *senha* |
| *tenant_id*                         | Terceira linha de saída a partir de `az ad sp` criar comando - *inquilino* |
| *subscription_id*                   | Saída do `az account show` comando |
| *managed_image_resource_group_name* | Nome do grupo de recursos que criou no primeiro passo |
| *managed_image_name*                | Nome para a imagem gerida do disco que é criada |


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

Este modelo constrói uma imagem Ubuntu 16.04 LTS, instala NGINX e, em seguida, desprovisiona o VM.

> [!NOTE]
> Se expandir neste modelo para obter credenciais de utilizador, ajuste o comando do provisionador que desprovisionia o agente Azure para ler `-deprovision` em vez de `deprovision+user` .
> A `+user` bandeira remove todas as contas do utilizador da fonte VM.


## <a name="build-packer-image"></a>Construa a imagem do Packer
Se ainda não tiver o Packer instalado na sua máquina local, [siga as instruções de instalação do Packer](https://www.packer.io/docs/install).

Construa a imagem especificando o seu ficheiro de modelo Packer da seguinte forma:

```bash
./packer build ubuntu.json
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```output
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

Leva alguns minutos para packer construir o VM, executar os provisões, e limpar a implementação.


## <a name="create-vm-from-azure-image"></a>Criar VM a partir da Imagem Azure
Agora pode criar um VM a partir da sua Imagem com [criação az vm](/cli/azure/vm). Especifique a Imagem que criou com o `--image` parâmetro. O exemplo a seguir cria um VM chamado *myVM* do *myPackerImage* e gera chaves SSH se ainda não existirem:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Se desejar criar VMs num grupo ou região de recursos diferente do que a sua imagem Packer, especifique o ID de imagem em vez do nome da imagem. Pode obter o ID de imagem com [um mostrador de imagem az](/cli/azure/image#az-image-show).

Leva alguns minutos para criar o VM. Uma vez criado o VM, tome nota do `publicIpAddress` mostrado pelo CLI Azure. Este endereço é utilizado para aceder ao site do NGINX através de um navegador web.

Para permitir que o tráfego da Web aceda à VM, abra a porta 80 a partir da Internet com [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Teste VM e NGINX
Agora, pode abrir um browser e introduzir `http://publicIpAddress` na barra de endereço. Forneça o seu próprio endereço IP público a partir do processo de criação da VM. A página NGINX predefinida é apresentada como no seguinte exemplo:

![Site predefinido do NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Passos seguintes
Também pode utilizar scripts de provisionadores Packer existentes com [O Azure Image Builder](image-builder.md).
