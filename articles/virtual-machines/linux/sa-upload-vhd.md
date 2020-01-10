---
title: Carregar um disco personalizado do Linux com o CLI do Azure
description: Criar e carregar um VHD (disco rígido virtual) no Azure usando o modelo de implantação do Resource Manager e o CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 6d1dd8f749f6c3e991413628bd1e08baf76a02f8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458669"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Carregar e criar uma VM do Linux de um disco personalizado com o CLI do Azure

Este artigo mostra como carregar um VHD (disco rígido virtual) para uma conta de armazenamento do Azure com o CLI do Azure e criar VMs do Linux a partir desse disco personalizado. Essa funcionalidade permite que você instale e configure um distribuição do Linux com seus requisitos e, em seguida, use esse VHD para criar rapidamente VMs (máquinas virtuais) do Azure.

Este tópico usa contas de armazenamento para os VHDs finais, mas você também pode executar essas etapas usando [discos gerenciados](upload-vhd.md). 

## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha os comandos de base para carregar um VHD no Azure. Informações mais detalhadas e contexto para cada etapa podem encontrar o restante do documento, [começando aqui](#requirements).

Verifique se você tem as [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Os nomes de parâmetro de exemplo incluídos `myResourceGroup`, `mystorageaccount`e `mydisks`.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Crie uma conta de armazenamento para manter seus discos virtuais com a [conta de armazenamento AZ Create](/cli/azure/storage/account). O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Liste as chaves de acesso para sua conta de armazenamento com a [lista de chaves de conta de armazenamento AZ](/cli/azure/storage/account/keys). Anote `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Crie um contêiner dentro de sua conta de armazenamento usando a chave de armazenamento obtida com [AZ Storage container Create](/cli/azure/storage/container). O exemplo a seguir cria um contêiner chamado `mydisks` usando o valor da chave de armazenamento de `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Por fim, carregue o VHD para o contêiner que você criou com [AZ Storage blob upload](/cli/azure/storage/blob). Especifique o caminho local para o VHD em `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Especifique o URI para o disco (`--image`) com [AZ VM Create](/cli/azure/vm). O exemplo a seguir cria uma VM chamada `myVM` usando o disco virtual carregado anteriormente:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A conta de armazenamento de destino deve ser a mesma em que você carregou o disco virtual. Você também precisa especificar ou responder a prompts para, todos os parâmetros adicionais exigidos pelo comando **AZ VM Create** , como rede virtual, endereço IP público, nome de usuário e chaves SSH. Você pode ler mais sobre os [parâmetros disponíveis do Resource Manager da CLI](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para concluir as etapas a seguir, você precisará de:

* **Sistema operacional Linux instalado em um arquivo. vhd** -instale uma [distribuição do Linux endossada pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consulte [informações para distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e um VHD:
  * Instale e configure o [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou o [KVM](https://www.linux-kvm.org/page/RunningKVM), tomando cuidado para usar o VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
  * Você também pode usar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Não há suporte para o formato VHDX mais recente no Azure. Ao criar uma VM, especifique o VHD como o formato. Se necessário, você pode converter discos VHDX para VHD usando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o cmdlet [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell. Além disso, o Azure não dá suporte ao carregamento de VHDs dinâmicos, portanto, você precisa converter esses discos em VHDs estáticos antes de carregá-los. Você pode usar ferramentas como os [utilitários do VHD do Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de carregamento para o Azure.
> 
> 

* As VMs criadas a partir de seu disco personalizado devem residir na mesma conta de armazenamento que o próprio disco
  * Criar uma conta de armazenamento e um contêiner para armazenar o disco personalizado e as VMs criadas
  * Depois de criar todas as suas VMs, você pode excluir seu disco com segurança

Verifique se você tem as [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Os nomes de parâmetro de exemplo incluídos `myResourceGroup`, `mystorageaccount`e `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Preparar o disco a ser carregado
O Azure dá suporte a várias distribuições do Linux (consulte [distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os artigos a seguir orientam você sobre como preparar as várias distribuições do Linux com suporte no Azure:

* **[Distribuições baseadas em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Outros-distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Consulte também as **[notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes)** para obter dicas mais gerais sobre como preparar imagens do Linux para o Azure.

> [!NOTE]
> O [SLA da plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se às VMs que executam o Linux somente quando uma das distribuições endossadas é usada com os detalhes de configuração, conforme especificado em ' versões com suporte ' no [Linux em distribuições endossadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Os grupos de recursos reúnem logicamente todos os recursos do Azure para dar suporte às suas máquinas virtuais, como a rede virtual e o armazenamento. Para obter mais informações sobre grupos de recursos, consulte [visão geral dos grupos de recursos](../../azure-resource-manager/management/overview.md). Antes de carregar seu disco personalizado e criar VMs, primeiro você precisa criar um grupo de recursos com [AZ Group Create](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Criar uma conta do Storage

Crie uma conta de armazenamento para seu disco personalizado e VMs com [AZ Storage Account Create](/cli/azure/storage/account). Todas as VMs com discos não gerenciados que você cria de seu disco personalizado precisam estar na mesma conta de armazenamento que esse disco. 

O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount` no grupo de recursos criado anteriormente:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Listar chaves de conta de armazenamento
O Azure gera chaves de acesso de 2 512 bits para cada conta de armazenamento. Essas chaves de acesso são usadas durante a autenticação na conta de armazenamento, como para realizar operações de gravação. Para obter mais informações sobre chaves de acesso da conta de armazenamento, consulte [gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md). Exiba as chaves de acesso com a [lista de chaves de conta de armazenamento AZ](/cli/azure/storage/account/keys).

Exiba as chaves de acesso para a conta de armazenamento que você criou:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

O resultado é semelhante a:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Anote `key1`, pois você irá usá-lo para interagir com sua conta de armazenamento nas próximas etapas.

## <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Da mesma forma que você cria diretórios diferentes para organizar logicamente o sistema de arquivos local, você cria contêineres em uma conta de armazenamento para organizar seus discos. Uma conta de armazenamento pode conter qualquer número de contêineres. Crie um contêiner com [AZ Storage container Create](/cli/azure/storage/container).

O exemplo a seguir cria um contêiner chamado `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Carregar VHD
Agora carregue seu disco personalizado com [AZ Storage blob upload](/cli/azure/storage/blob). Você carrega e armazena seu disco personalizado como um blob de páginas.

Especifique sua chave de acesso, o contêiner que você criou na etapa anterior e o caminho para o disco personalizado no computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Crie a VM
Para criar uma VM com discos não gerenciados, especifique o URI para o disco (`--image`) com [AZ VM Create](/cli/azure/vm). O exemplo a seguir cria uma VM chamada `myVM` usando o disco virtual carregado anteriormente:

Especifique o parâmetro `--image` com [AZ VM Create](/cli/azure/vm) para apontar para seu disco personalizado. Verifique se `--storage-account` corresponde à conta de armazenamento onde o disco personalizado está armazenado. Você não precisa usar o mesmo contêiner que o disco personalizado para armazenar suas VMs. Certifique-se de criar contêineres adicionais da mesma maneira que as etapas anteriores antes de carregar o disco personalizado.

O exemplo a seguir cria uma VM chamada `myVM` de seu disco personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Você ainda precisa especificar ou responder a prompts para, todos os parâmetros adicionais exigidos pelo comando **AZ VM Create** , como nome de usuário e chaves SSH.


## <a name="resource-manager-template"></a>Modelo do Resource Manager
Os modelos de Azure Resource Manager são arquivos JavaScript Object Notation (JSON) que definem o ambiente que você deseja criar. Os modelos são divididos em diferentes provedores de recursos, como computação ou rede. Você pode usar modelos existentes ou escrever seus próprios. Leia mais sobre como [usar o Gerenciador de recursos e modelos](../../azure-resource-manager/management/overview.md).

Dentro do provedor de `Microsoft.Compute/virtualMachines` do seu modelo, você tem um nó de `storageProfile` que contém os detalhes de configuração para sua VM. Os dois parâmetros principais a serem editados são os URIs `image` e `vhd` que apontam para seu disco personalizado e o disco virtual da nova VM. Veja a seguir um exemplo de JSON para usar um disco personalizado:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Você pode usar [esse modelo existente para criar uma VM com base em uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou ler sobre como [criar seus próprios modelos de Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). 

Quando tiver um modelo configurado, use [AZ Group Deployment Create](/cli/azure/group/deployment) para criar suas VMs. Especifique o URI do seu modelo JSON com o parâmetro `--template-uri`:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Se você tiver um arquivo JSON armazenado localmente em seu computador, poderá usar o parâmetro `--template-file` em vez disso:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Passos seguintes
Depois de preparar e carregar seu disco virtual personalizado, você pode ler mais sobre como [usar o Resource Manager e modelos](../../azure-resource-manager/management/overview.md). Talvez você também queira [Adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) às novas VMS. Se você tiver aplicativos em execução nas VMs que precisa acessar, certifique-se de [abrir portas e pontos de extremidade](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

