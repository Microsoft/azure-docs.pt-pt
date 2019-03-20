---
title: Carregar ou copiar uma VM com Linux personalizada com a CLI do Azure | Documentos da Microsoft
description: Carregar ou copiar uma máquina virtual personalizada com o modelo de implementação do Resource Manager e a CLI do Azure
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
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 6483fa8737ee3de6a60c4e4646fefec30ae702b6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006641"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Criar uma VM do Linux a partir de um disco personalizado com a CLI do Azure

<!-- rename to create-vm-specialized -->

Este artigo mostra-lhe como carregar um disco de rígido virtual (VHD) personalizado e como copiar um VHD existente no Azure. O VHD criado recentemente, em seguida, é utilizado para criar novas máquinas de virtuais de Linux (VMs). Pode instalar e configurar uma distribuição Linux os seus requisitos e, em seguida, utilizar esse VHD para criar uma nova máquina virtual do Azure.

Para criar várias VMs a partir do seu disco personalizado, primeiro de criar uma imagem da sua VM ou o VHD. Para obter mais informações, consulte [criar uma imagem personalizada de uma VM do Azure com a CLI](tutorial-custom-images.md).

Tem duas opções para criar um disco personalizado:
* Carregar um VHD
* Copiar uma VM do Azure existente

## <a name="quick-commands"></a>Comandos rápidos

Quando criar uma VM nova com [az vm crie](/cli/azure/vm#az-vm-create) partir de um disco especializado ou personalizado, **anexar** o disco (– anexar-disco de SO) em vez de especificar uma imagem personalizada ou do marketplace (– imagem). O exemplo seguinte cria uma VM com o nome *myVM* com o disco gerido com o nome *myManagedDisk* criados a partir de seu VHD personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Requisitos
Para concluir os seguintes passos, terá de:

* Uma máquina virtual do Linux que tenha sido preparada para utilização no Azure. O [preparar a VM](#prepare-the-vm) secção deste artigo inclui como localizar informações de distro específicas sobre como instalar o agente do Linux do Azure (waagent), que é necessário para estabelecer ligação a uma VM com SSH.
* O ficheiro VHD existente de um [distribuição do Linux apoiadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consulte [informações sobre distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) para um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e o VHD:
  * Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](https://www.linux-kvm.org/page/RunningKVM), tendo cuidado para utilizar o VHD como seu formato de imagem. Se for necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) com `qemu-img convert`.
  * Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O formato mais recente do VHDX não é suportado no Azure. Quando cria uma VM, especifique o VHD como o formato. Se for necessário, pode converter discos VHDX em VHD com o [qemu img converter](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Azure não suporta o carregamento de VHDs dinâmicos, por isso, precisará converter esses discos para VHDs estáticos antes de carregar. Pode usar ferramentas como [utilitários de VHD do Azure para GO](https://github.com/Microsoft/azure-vhd-utils-for-go) converter discos dinâmicos durante o processo de carregamento-los para o Azure.
> 
> 


* Certifique-se de que tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e tem sessão iniciada numa conta do Azure com [início de sessão az](/cli/azure/reference-index#az-login).

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores, como *myResourceGroup*, *mystorageaccount*, e *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparar a VM

O Azure suporta várias distribuições de Linux (veja [distribuições apoiadas pelo](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os seguintes artigos descrevem como preparar as várias distribuições de Linux que são suportadas no Azure:

* [Distribuições baseada em centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Outros: Distribuições não aprovadas pelo Azure](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Consulte também os [observações de instalação de Linux](create-upload-generic.md#general-linux-installation-notes) para obter dicas mais gerais sobre como preparar imagens do Linux para o Azure.

> [!NOTE]
> O [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se para VMs que executam o Linux apenas quando uma das distribuições apoiadas é usada com os detalhes de configuração conforme especificado nas "Versões suportadas" no [Linux em apoiadas pelo Azure Distribuições](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opção 1: Carregar um VHD

Pode carregar um VHD personalizado que tem em execução num computador local ou que tenha exportado a partir de outra cloud. Para utilizar um VHD para criar uma nova VM do Azure, terá de carregar o VHD para uma conta de armazenamento e criar um disco gerido a partir do VHD. Para obter mais informações, veja [Azure Managed Disks overview (Descrição geral dos Managed Disks do Azure)](../windows/managed-disks-overview.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de carregar o seu disco personalizado e criação de VMs, terá de criar um grupo de recursos com [criar grupo az](/cli/azure/group#az-group-create).

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma conta de armazenamento para o seu disco personalizada e as VMs com [criar conta de armazenamento az](/cli/azure/storage/account). O exemplo seguinte cria uma conta de armazenamento com o nome *mystorageaccount* no grupo de recursos criado anteriormente:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Listar chaves de conta de armazenamento
Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Estas chaves de acesso são utilizadas durante a autenticação para a conta de armazenamento, como quando realização de operações de escrita. Para obter mais informações, consulte [gerir o acesso a armazenamento](../../storage/common/storage-account-manage.md#access-keys). 

Exibir as chaves de acesso sem [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#az-storage-account-keys-list). Por exemplo, para ver as teclas de acesso para o armazenamento de conta que criou:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
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
Anote **chave1** como irá utilizá-lo para interagir com a sua conta de armazenamento nos passos seguintes.

### <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Da mesma forma que criar diretórios diferentes para organizar logicamente o seu sistema de arquivos local, irá criar contentores numa conta de armazenamento para organizar os seus discos. Uma conta de armazenamento pode conter o número de contentores. Criar um contentor com o [criar contentor de armazenamento az](/cli/azure/storage/container#az-storage-container-create).

O exemplo seguinte cria um contentor com o nome *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Carregar o VHD
Carregar o seu disco personalizado com [carregamento de BLOBs de armazenamento az](/cli/azure/storage/blob#az-storage-blob-upload). Irá carregar e armazenar o disco personalizada como um blob de página.

Especifique a chave de acesso, o contentor que criou no passo anterior e, em seguida, o caminho para o disco personalizado no seu computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Carregar o VHD, poderá demorar algum tempo.

### <a name="create-a-managed-disk"></a>Criar um disco gerido


Criar um disco gerido a partir de VHD com o [criar disco de az](/cli/azure/disk#az-disk-create). O exemplo seguinte cria um disco gerido com o nome *myManagedDisk* a partir de VHD que carregou para a conta de armazenamento com o nome e o contentor:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Opção 2: Copiar uma VM existente

Também pode criar uma VM personalizada no Azure e, em seguida, copie o disco do SO e anexá-lo a uma nova VM para criar outra cópia. Esta situação funciona para testes, mas se pretender utilizar uma VM do Azure existente como o modelo para várias novas VMs, crie uma *imagem* em vez disso. Para obter mais informações sobre como criar uma imagem de VM do Azure existente, consulte [criar uma imagem personalizada de uma VM do Azure com a CLI](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Criar um instantâneo

Este exemplo cria um instantâneo de uma VM com o nome *myVM* no grupo de recursos *myResourceGroup* e cria um instantâneo com o nome *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Criar o disco gerido

Crie um novo disco gerido a partir do instantâneo.

Obtenha o ID do instantâneo. Neste exemplo, o instantâneo é denominado *osDiskSnapshot* e está a ser a *myResourceGroup* grupo de recursos.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Crie o disco gerido. Neste exemplo, vamos criar um disco gerido com o nome *myManagedDisk* da nossa instantâneo, onde o disco está no armazenamento standard e o tamanho em 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Crie a VM

Criar a sua VM com [az vm criar](/cli/azure/vm#az-vm-create) e anexar (– anexar-disco de SO) do disco gerido como disco do SO. O exemplo seguinte cria uma VM com o nome *myNewVM* com o disco gerido que criou a partir do VHD carregado:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Deve ser capaz de SSH na VM com as credenciais da origem de VM. 

## <a name="next-steps"></a>Passos Seguintes
Depois de ter preparado e carregado o seu disco virtual personalizado, pode ler mais sobre [utilizando o Gestor de recursos e modelos](../../azure-resource-manager/resource-group-overview.md). Talvez também queira [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) às suas VMs novas. Se tiver aplicações em execução nas suas VMs que precisam de aceder, não se esqueça [abrir portas e pontos de extremidade](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
