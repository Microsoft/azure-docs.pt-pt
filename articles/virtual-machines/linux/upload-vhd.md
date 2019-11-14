---
title: Carregar ou copiar uma VM Linux personalizada com CLI do Azure
description: Carregar ou copiar uma máquina virtual personalizada usando o modelo de implantação do Gerenciador de recursos e o CLI do Azure
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
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 95486208f52b2faa2fbb3db5bf1ef968c330dab6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034292"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Criar uma VM do Linux de um disco personalizado com o CLI do Azure

<!-- rename to create-vm-specialized -->

Este artigo mostra como carregar um VHD (disco rígido virtual) personalizado e como copiar um VHD existente no Azure. Em seguida, o VHD recém-criado é usado para criar novas VMs (máquinas virtuais) do Linux. Você pode instalar e configurar um distribuição do Linux para seus requisitos e, em seguida, usar esse VHD para criar uma nova máquina virtual do Azure.

Para criar várias VMs a partir de seu disco personalizado, primeiro crie uma imagem de sua VM ou VHD. Para obter mais informações, consulte [criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).

Você tem duas opções para criar um disco personalizado:
* Carregar um VHD
* Copiar uma VM do Azure existente


## <a name="requirements"></a>Requisitos
Para concluir as etapas a seguir, você precisará de:

- Uma máquina virtual Linux que foi preparada para uso no Azure. A seção [preparar a VM](#prepare-the-vm) deste artigo aborda como encontrar informações específicas de distribuição sobre como instalar o agente Linux do Azure (waagent), que é necessário para que você se conecte a uma VM com o SSH.
- O arquivo VHD de uma [distribuição do Linux endossada do Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) existente (ou consulte [informações para distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) para um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e um VHD:
  - Instale e configure o [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou o [KVM](https://www.linux-kvm.org/page/RunningKVM), tomando cuidado para usar o VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) com `qemu-img convert`.
  - Você também pode usar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Não há suporte para o formato VHDX mais recente no Azure. Ao criar uma VM, especifique o VHD como o formato. Se necessário, você pode converter discos VHDX em VHD com o cmdlet [QEMU-img Convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) do PowerShell. O Azure não dá suporte ao carregamento de VHDs dinâmicos, portanto, você precisará converter esses discos em VHDs estáticos antes de carregá-los. Você pode usar ferramentas como os [utilitários do VHD do Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de carregá-los no Azure.
> 
> 


- Verifique se você tem as [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e se está conectado a uma conta do Azure com [AZ login](/cli/azure/reference-index#az-login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores, como `myResourceGroup`, `mystorageaccount`e `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparar a VM

O Azure dá suporte a várias distribuições do Linux (consulte [distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os artigos a seguir descrevem como preparar as várias distribuições do Linux com suporte no Azure:

* [Distribuições baseadas em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES e openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Outros: distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Consulte também as [notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter dicas mais gerais sobre como preparar imagens do Linux para o Azure.

> [!NOTE]
> O [SLA da plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se às VMs que executam o Linux somente quando uma das distribuições endossadas é usada com os detalhes de configuração, conforme especificado em "versões com suporte" no [Linux em distribuições endossadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opção 1: carregar um VHD

Agora você pode carregar o VHD diretamente em um disco gerenciado. Para obter instruções, consulte [carregar um VHD no Azure usando CLI do Azure](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Opção 2: copiar uma VM existente

Você também pode criar uma VM personalizada no Azure e, em seguida, copiar o disco do sistema operacional e anexá-lo a uma nova VM para criar outra cópia. Isso é bom para o teste, mas se você quiser usar uma VM do Azure existente como modelo para várias novas VMs, crie uma *imagem* em vez disso. Para obter mais informações sobre como criar uma imagem de uma VM do Azure existente, consulte [criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).

Se você quiser copiar uma VM existente para outra região, talvez queira usar azcopy para criar [uma cópia de um disco em outra região](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

Caso contrário, você deve tirar um instantâneo da VM e, em seguida, criar um novo VHD do sistema operacional a partir do instantâneo.

### <a name="create-a-snapshot"></a>Criar um instantâneo

Este exemplo cria um instantâneo de uma VM chamada *myVM* no grupo de recursos *MyResource* Group e cria um instantâneo chamado *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Criar o disco gerenciado

Crie um novo disco gerenciado a partir do instantâneo.

Obtenha a ID do instantâneo. Neste exemplo, o instantâneo é denominado *osDiskSnapshot* e está no grupo de recursos *MyResource* Group.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Crie o disco gerenciado. Neste exemplo, criaremos um disco gerenciado chamado *myManagedDisk* de nosso instantâneo, em que o disco está no armazenamento padrão e é dimensionado em 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Crie a VM

Crie sua VM com [AZ VM Create](/cli/azure/vm#az-vm-create) e anexe (--Attach-os-Disk) o disco gerenciado como o disco do sistema operacional. O exemplo a seguir cria uma VM denominada *myNewVM* usando o disco gerenciado que você criou a partir do VHD carregado:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Você deve ser capaz de fazer SSH na VM com as credenciais da VM de origem. 

## <a name="next-steps"></a>Passos seguintes
Depois de preparar e carregar seu disco virtual personalizado, você pode ler mais sobre como [usar o Resource Manager e modelos](../../azure-resource-manager/resource-group-overview.md). Talvez você também queira [Adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) às novas VMS. Se você tiver aplicativos em execução nas VMs que precisa acessar, certifique-se de [abrir portas e pontos de extremidade](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
