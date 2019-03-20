---
title: Carregar um disco de Linux personalizado com a CLI do Azure | Documentos da Microsoft
description: Criar e carregar um disco rígido virtual (VHD) para o Azure com o modelo de implementação do Resource Manager e a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
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
ms.openlocfilehash: 368fec658dd1f063c45f3d00d42a4549ca9dfd83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996741"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Carregar e criar uma VM do Linux a partir de discos personalizados com a CLI do Azure

Este artigo mostra-lhe como carregar um disco rígido virtual (VHD) a uma conta de armazenamento do Azure com a CLI do Azure e criar VMs do Linux a partir deste disco personalizado. Esta funcionalidade permite-lhe instalar e configurar uma distribuição Linux os seus requisitos e, em seguida, utilize esse VHD para criar rapidamente máquinas virtuais do Azure (VMs).

Este tópico utiliza contas de armazenamento para os VHDs finais, mas também o pode fazer estes passos com [discos geridos](upload-vhd.md). 

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, os seguintes detalhes para a secção de segurança da base de comandos para carregar um VHD para o Azure. Mais informações detalhadas e contexto de cada etapa pode ver o resto do documento, [a partir de aqui](#requirements).

Certifique-se de que tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `mydisks`.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Criar uma conta de armazenamento para armazenar os discos virtuais com [criar conta de armazenamento az](/cli/azure/storage/account). O exemplo seguinte cria uma conta de armazenamento com o nome `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Listar as chaves de acesso para a sua conta de armazenamento com [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys). Tome nota do `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Criar um contentor na sua conta de armazenamento com a chave de armazenamento que obteve com [criar contentor de armazenamento az](/cli/azure/storage/container). O exemplo seguinte cria um contentor com o nome `mydisks` utilizando o valor da chave armazenamento `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Por fim, carregue o VHD para o contentor que criou com [carregamento de BLOBs de armazenamento az](/cli/azure/storage/blob). Especifique o caminho local para o VHD em `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Especifique o URI para o disco (`--image`) com [az vm criar](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome `myVM` através do disco virtual carregado anteriormente:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A conta de armazenamento de destino tem de ser o mesmo que em que o seu disco virtual para que carregou. Também tem de especificar ou resposta pede-lhe para todos os parâmetros adicionais, necessários para o **az vm criar** comando, como a rede virtual, endereço IP público, nome de utilizador e as chaves SSH. Pode ler mais sobre o [parâmetros disponíveis do Gestor de recursos de CLI](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para concluir os passos seguintes, tem de:

* **Sistema de operativo de Linux instalado num arquivo. vhd** -instalar uma [distribuição do Linux apoiadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou veja [informações sobre distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) para um disco virtual no formato VHD . Existem várias ferramentas para criar uma VM e o VHD:
  * Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](https://www.linux-kvm.org/page/RunningKVM), tendo cuidado para utilizar o VHD como seu formato de imagem. Se for necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
  * Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O formato mais recente do VHDX não é suportado no Azure. Quando cria uma VM, especifique o VHD como o formato. Se for necessário, pode converter discos VHDX para VHD utilizando [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou a [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, o Azure não suporta a carregar VHDs dinâmicos, por isso terá de converter esses discos para VHDs estáticos antes de carregar. Pode usar ferramentas como [utilitários de VHD do Azure para GO](https://github.com/Microsoft/azure-vhd-utils-for-go) converter discos dinâmicos durante o processo de carregamento para o Azure.
> 
> 

* As VMs criadas a partir do seu disco personalizado tem de residir na mesma conta de armazenamento como o próprio disco
  * Criar uma conta de armazenamento e um contentor para conter o seu disco personalizada e VMs criadas
  * Depois de criar todas as suas VMs, pode eliminar em segurança o disco

Certifique-se de que tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Preparar o disco a ser carregado
O Azure suporta várias distribuições de Linux (veja [distribuições apoiadas pelo](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os seguintes artigos descreve como preparar as várias distribuições de Linux que são suportadas no Azure:

* **[Distribuições baseada em centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Outros - distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Consulte também os **[observações de instalação de Linux](create-upload-generic.md#general-linux-installation-notes)** para obter dicas mais gerais sobre como preparar imagens do Linux para o Azure.

> [!NOTE]
> O [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se para VMs que executam o Linux apenas quando uma das distribuições apoiadas é usada com os detalhes de configuração conforme especificado nas versões suportadas em [Linux em apoiadas pelo Azure Distribuições](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Grupos de recursos logicamente reunir todos os recursos do Azure para suportar as suas máquinas virtuais, como o sistema de rede virtual e o armazenamento. Para obter mais grupos de recursos de informações, consulte [descrição geral de grupos de recursos](../../azure-resource-manager/resource-group-overview.md). Antes de carregar o seu disco personalizado e criação de VMs, tem primeiro de criar um grupo de recursos com [criar grupo az](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma conta de armazenamento para o seu disco personalizada e as VMs com [criar conta de armazenamento az](/cli/azure/storage/account). Todas as VMs com discos não geridos que criar a partir de sua necessidade de disco personalizada para estar na mesma conta de armazenamento desse disco. 

O exemplo seguinte cria uma conta de armazenamento com o nome `mystorageaccount` no grupo de recursos criado anteriormente:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Listar chaves de conta de armazenamento
Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Essas chaves de acesso são utilizados durante a autenticação da conta de armazenamento, por exemplo, para realizar operações de escrita. Leia mais sobre [gerir o acesso a armazenamento aqui](../../storage/common/storage-account-manage.md#access-keys). Exibir as chaves de acesso sem [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys).

Ver as chaves de acesso para a conta de armazenamento que criou:

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
Tome nota do `key1` como irá utilizá-lo para interagir com a sua conta de armazenamento nos passos seguintes.

## <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Da mesma forma que criar diretórios diferentes para organizar logicamente o seu sistema de arquivos local, criar contentores numa conta de armazenamento para organizar os seus discos. Uma conta de armazenamento pode conter qualquer número de contentores. Criar um contentor com o [criar contentor de armazenamento az](/cli/azure/storage/container).

O exemplo seguinte cria um contentor com o nome `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Carregar o VHD
Agora carregue seus discos personalizados com [carregamento de BLOBs de armazenamento az](/cli/azure/storage/blob). Carregar e armazenar o disco personalizada como um blob de página.

Especifique a chave de acesso, o contentor que criou no passo anterior e, em seguida, o caminho para o disco personalizado no seu computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Crie a VM
Para criar uma VM com discos não geridos, especifique o URI para o disco (`--image`) com [az vm criar](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome `myVM` através do disco virtual carregado anteriormente:

Especificar a `--image` parâmetro com [az vm criar](/cli/azure/vm) para apontar para o seu disco personalizado. Certifique-se de que `--storage-account` corresponde à conta de armazenamento onde o seu disco personalizado é armazenado. Não é necessário utilizar o mesmo contentor que o disco personalizado para armazenar as suas VMs. Certifique-se criar quaisquer contentores adicionais da mesma forma como os passos anteriores, antes de carregar o seu disco personalizado.

O exemplo seguinte cria uma VM com o nome `myVM` do seu disco personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Ainda tem de especificar ou resposta pede-lhe para todos os parâmetros adicionais, necessários para o **az vm criar** comando, como o nome de utilizador e as chaves SSH.


## <a name="resource-manager-template"></a>Modelo do Resource Manager
Modelos Azure Resource Manager são ficheiros de JavaScript Object Notation (JSON) que definem o ambiente que pretende criar. Os modelos são divididos em fornecedores de recursos diferente, como a rede ou computação. Pode utilizar modelos existentes ou escreva o seu próprio. Leia mais sobre [utilizando o Gestor de recursos e modelos](../../azure-resource-manager/resource-group-overview.md).

Dentro de `Microsoft.Compute/virtualMachines` fornecedor do seu modelo, tem um `storageProfile` nó que contém os detalhes de configuração para a sua VM. São os dois parâmetros de principais para editar a `image` e `vhd` URIs que apontam para o disco de personalizado e o disco virtual da sua nova VM. O código a seguir mostra um exemplo de JSON para utilizar um disco personalizado:

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

Pode usar [este modelo existente para criar uma VM a partir de uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou leitura sobre [criando seus próprios modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Assim que tiver um modelo de configurado, utilize [criar a implementação do grupo az](/cli/azure/group/deployment) para criar as suas VMs. Especifique o URI do seu modelo JSON com o `--template-uri` parâmetro:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Se tiver um ficheiro JSON armazenado localmente no seu computador, pode usar o `--template-file` parâmetro em vez disso:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Passos Seguintes
Depois de ter preparado e carregado o seu disco virtual personalizado, pode ler mais sobre [utilizando o Gestor de recursos e modelos](../../azure-resource-manager/resource-group-overview.md). Talvez também queira [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) às suas VMs novas. Se tiver aplicações em execução nas suas VMs que precisam de aceder, não se esqueça [abrir portas e pontos de extremidade](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

