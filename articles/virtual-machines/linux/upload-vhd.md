---
title: Faça upload ou copie um Linux VM personalizado com Azure CLI
description: Faça upload ou copie uma máquina virtual personalizada utilizando o modelo de implementação do Gestor de Recursos e o CLI Azure
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 9d549d77b4a60f7543f69a9fd89e8b538c95d010
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564616"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Criar um Linux VM a partir de um disco personalizado com o Azure CLI

<!-- rename to create-vm-specialized -->

Este artigo mostra-lhe como carregar um disco rígido virtual personalizado (VHD) e como copiar um VHD existente em Azure. O VHD recém-criado é então usado para criar novas máquinas virtuais Linux (VMs). Pode instalar e configurar um Linux distro para os seus requisitos e, em seguida, usar esse VHD para criar uma nova máquina virtual Azure.

Para criar vários VMs a partir do seu disco personalizado, crie primeiro uma imagem a partir do seu VM ou VHD. Para obter mais informações, consulte [Criar uma imagem personalizada de um VM Azure utilizando o CLI](tutorial-custom-images.md).

Tem duas opções para criar um disco personalizado:
* Carregar um VHD
* Copiar um Azure VM existente


## <a name="requirements"></a>Requisitos
Para completar os seguintes passos, você precisará:

- Uma máquina virtual Linux que foi preparada para ser utilizada em Azure. A secção [VM](#prepare-the-vm) deste artigo abrange como encontrar informações específicas sobre a instalação do Agente Azure Linux (waagent), que é necessário para que possa ligar-se a um VM com SSH.
- O ficheiro VHD de uma [distribuição Linux existente endossada pelo Azure](endorsed-distros.md) (ou ver [informações para distribuições não endossadas)](create-upload-generic.md)para um disco virtual no formato VHD. Existem várias ferramentas para criar um VM e VHD:
  - Instale e configuure [o QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [o KVM,](https://www.linux-kvm.org/page/RunningKVM)tendo o cuidado de utilizar o VHD como formato de imagem. Se necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) com `qemu-img convert` .
  - Também pode utilizar o Hyper-V [no Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) ou [no Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

> [!NOTE]
> O novo formato VHDX não é suportado no Azure. Quando criar um VM, especifique o VHD como o formato. Se necessário, pode converter discos VHDX em VHD com [conversão qemu-img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [cmdlet Converte-VHD](/powershell/module/hyper-v/convert-vhd) PowerShell. O Azure não suporta o upload de VHDs dinâmicos, por isso terás de converter esses discos em VHDs estáticos antes de carregar. Pode utilizar ferramentas como [Azure VHD Utilities para GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de upload para Azure.
> 
> 


- Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e está inscrito numa conta Azure com [login az](/cli/azure/reference-index#az-login).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores, tais como `myResourceGroup` `mystorageaccount` , e `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparar a VM

A Azure suporta várias distribuições Linux (ver [Distribuição Endossada).](endorsed-distros.md) Os seguintes artigos descrevem como preparar as várias distribuições Linux que são suportadas no Azure:

* [Distribuições baseadas em CentOS](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES e openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Outros: Distribuições Não Endossadas](create-upload-generic.md)

Consulte também as [Notas de Instalação Linux](create-upload-generic.md#general-linux-installation-notes) para obter dicas mais gerais sobre a preparação das imagens Linux para Azure.

> [!NOTE]
> A [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se aos VMs que executam o Linux apenas quando uma das distribuições endossadas é utilizada com os detalhes de configuração especificados em "Versões Suportadas" em [Linux em Azure-Endorsed Distribuição](endorsed-distros.md).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opção 1: Carregar um VHD

Agora pode carregar o VHD diretamente para um disco gerido. Para obter instruções, consulte [o Upload a VHD to Azure utilizando o Azure CLI](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Opção 2: Copiar um VM existente

Também pode criar um VM personalizado em Azure e, em seguida, copiar o disco DE e anexá-lo a um novo VM para criar outra cópia. Isto é bom para testes, mas se quiser usar um Azure VM existente como modelo para vários novos VMs, crie uma *imagem* em vez disso. Para obter mais informações sobre a criação de uma imagem a partir de um VM Azure existente, consulte [Criar uma imagem personalizada de um Azure VM utilizando o CLI](tutorial-custom-images.md).

Se quiser copiar um VM existente para outra região, talvez queira utilizar a azcopia para [creat uma cópia de um disco noutra região.](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 

Caso contrário, deve tirar uma foto do VM e, em seguida, criar um novo OS VHD a partir da foto.

### <a name="create-a-snapshot"></a>Criar um instantâneo

Este exemplo cria uma imagem instantânea de um VM chamado *myVM* no grupo de recursos *myResourceGroup* e cria um instantâneo chamado *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Criar o disco gerido

Crie um novo disco gerido a partir do instantâneo.

Pegue a identificação da foto. Neste exemplo, o instantâneo é nomeado *osDiskSnapshot* e está no grupo de recursos *myResourceGroup.*

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

## <a name="create-the-vm"></a>Criar a VM

Crie o seu VM com [az vm criar](/cli/azure/vm#az-vm-create) e fixar (--attach-os-disk) o disco gerido como o disco DE. O exemplo a seguir cria um VM chamado *myNewVM* utilizando o disco gerido que criou a partir do seu VHD carregado:

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
Depois de ter preparado e carregado o seu disco virtual personalizado, pode ler mais sobre [a utilização de Gestor de Recursos e modelos.](../../azure-resource-manager/management/overview.md) Também pode querer [adicionar um disco de dados](add-disk.md) aos seus novos VMs. Se tiver aplicações em execução nos seus VMs a que necessita de aceder, certifique-se de [abrir portas e pontos finais](nsg-quickstart.md).
