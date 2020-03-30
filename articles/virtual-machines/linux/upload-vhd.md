---
title: Upload ou copiar um VM Linux personalizado com O ClI Azure
description: Carregue ou copie uma máquina virtual personalizada utilizando o modelo de implementação do Gestor de Recursos e o Azure CLI
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
ms.openlocfilehash: b5063c8037a763c1919d2172a81c8abbbd406ace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060153"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Crie um VM Linux a partir de um disco personalizado com o Azure CLI

<!-- rename to create-vm-specialized -->

Este artigo mostra-lhe como carregar um disco rígido virtual personalizado (VHD) e como copiar um VHD existente em Azure. O VHD recém-criado é então usado para criar novas máquinas virtuais Linux (VMs). Pode instalar e configurar um distro linux às suas necessidades e, em seguida, utilizar esse VHD para criar uma nova máquina virtual Azure.

Para criar vários VMs a partir do seu disco personalizado, crie primeiro uma imagem a partir do seu VM ou VHD. Para mais informações, consulte [Criar uma imagem personalizada de um VM Azure utilizando o CLI](tutorial-custom-images.md).

Tem duas opções para criar um disco personalizado:
* Carregar um VHD
* Copiar um VM Azure existente


## <a name="requirements"></a>Requisitos
Para completar os seguintes passos, terá de ser:

- Uma máquina virtual Linux que foi preparada para ser usada em Azure. A secção Prepare a secção [VM](#prepare-the-vm) deste artigo abrange como encontrar informações específicas sobre a instalação do Agente Azure Linux (waagent), que é necessário para que possa ligar-se a um VM com SSH.
- O ficheiro VHD de uma [distribuição linux apoiada pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou ver [informações para distribuições não endossadas)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)para um disco virtual no formato VHD. Existem múltiplas ferramentas para criar um VM e VHD:
  - Instale e configure [qEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM,](https://www.linux-kvm.org/page/RunningKVM)tendo o cuidado de usar o VHD como formato de imagem. Se necessário, pode converter `qemu-img convert`uma [imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) com .
  - Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O novo formato VHDX não é suportado no Azure. Quando criar um VM, especifique o VHD como formato. Se necessário, pode converter discos VHDX em VHD com [conversão qemu-img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o cmdlet [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell. O Azure não suporta o upload de VHDs dinâmicos, pelo que terá de converter esses discos em VHDs estáticos antes de carregar. Pode utilizar ferramentas como [os Utilitários Azure VHD para a GO](https://github.com/Microsoft/azure-vhd-utils-for-go) converter discos dinâmicos durante o processo de upload para o Azure.
> 
> 


- Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e está inscrito numa conta Azure com [login az](/cli/azure/reference-index#az-login).

Nos exemplos seguintes, substitua os nomes dos `myResourceGroup` `mystorageaccount`parâmetros `mydisks`de exemplo pelos seus próprios valores, tais como , e .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparar a VM

O Azure suporta várias distribuições linux (ver [Distribuições Endossadas).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Os seguintes artigos descrevem como preparar as várias distribuições linux que são suportadas no Azure:

* [Distribuições baseadas em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES e openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Outros: Distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Consulte também as Notas de [Instalação Linux](create-upload-generic.md#general-linux-installation-notes) para obter dicas mais gerais sobre a preparação das imagens linux para o Azure.

> [!NOTE]
> A [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se aos VMs que executam o Linux apenas quando uma das distribuições endossadas for utilizada com os detalhes de configuração especificados em "Versões Suportadas" em [Linux em Distribuições Apoiadas pelo Azure.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opção 1: Carregar um VHD

Agora pode enviar VHD diretamente para um disco gerido. Para obter instruções, consulte [O upload de um VHD para Azure utilizando o Azure CLI](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Opção 2: Copiar um VM existente

Também pode criar um VM personalizado em Azure e, em seguida, copiar o disco OS e anexá-lo a um novo VM para criar outra cópia. Isto é bom para testes, mas se quiser usar um VM Azure existente como modelo para vários novos VMs, crie uma *imagem* em vez disso. Para obter mais informações sobre a criação de uma imagem a partir de um VM Azure existente, consulte [Criar uma imagem personalizada de um VM Azure utilizando o CLI](tutorial-custom-images.md).

Se quiser copiar um VM existente para outra região, talvez queira usar azcópia para [crear uma cópia de um disco noutra região](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

Caso contrário, deve tirar uma foto do VM e, em seguida, criar um novo VHD osso a partir do instantâneo.

### <a name="create-a-snapshot"></a>Criar um instantâneo

Este exemplo cria uma imagem de um VM chamado *myVM* no grupo de recursos *myResourceGroup* e cria um instantâneo chamado *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Criar o disco gerido

Crie um novo disco gerido a partir do instantâneo.

Pegue a identificação da foto. Neste exemplo, o instantâneo chama-se *osDiskSnapshot* e está no grupo de recursos *myResourceGroup.*

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Crie o disco gerido. Neste exemplo, criaremos um disco gerido chamado *myManagedDisk* a partir do nosso instantâneo, onde o disco está em armazenamento padrão e dimensionado a 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Crie a VM

Crie o seu VM com [az vm criar](/cli/azure/vm#az-vm-create) e anexar (-anexar-os-disco) o disco gerido como o disco OS. O exemplo seguinte cria um VM chamado *myNewVM* utilizando o disco gerido que criou a partir do seu VHD carregado:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Você deve ser capaz de SSH no VM com as credenciais da fonte VM. 

## <a name="next-steps"></a>Passos seguintes
Depois de ter preparado e carregado o seu disco virtual personalizado, pode ler mais sobre [a utilização do Gestor de Recursos e modelos](../../azure-resource-manager/management/overview.md). Também pode querer [adicionar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) de dados aos seus novos VMs. Se tiver aplicações em execução nos seus VMs a que precisa de aceder, certifique-se de [abrir portas e pontos finais.](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
