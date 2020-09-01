---
title: Crie imagens VM para o seu dispositivo GPU Azure Stack Edge
description: Descreve como criar imagens linux ou Windows VM para usar com o seu dispositivo GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: c7730b776ef1d7e1d5e372049395492641b839ff
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146016"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-device"></a>Crie imagens VM personalizadas para o seu dispositivo Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Para implementar VMs no seu dispositivo Azure Stack Edge, precisa de ser capaz de criar imagens VM personalizadas que pode utilizar para criar VMs. Este artigo descreve os passos necessários para criar imagens personalizadas do Linux ou Windows VM que pode utilizar para implantar VMs no seu dispositivo Azure Stack Edge.

## <a name="vm-image-workflow"></a>Fluxo de trabalho de imagem VM

O fluxo de trabalho requer que você crie uma máquina virtual em Azure, personalize o VM, generalize e, em seguida, descarregue o VHD correspondente ao VM. Este VHD generalizado é carregado para Azure Stack Edge, o disco gerido é criado a partir desse VHD, a imagem é criada a partir de disco gerido, e finalmente VMs são criados a partir dessa imagem.   

Para obter mais informações, aceda a [implementar um VM no seu dispositivo Azure Stack Edge utilizando a Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Crie uma imagem VM personalizada do Windows

Faça os seguintes passos para criar uma imagem VM do Windows.

1. Criar uma máquina virtual windows. Para mais informações, vá ao [Tutorial: Crie e gere vMs do Windows com a Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Descarregue um disco operativo existente.

    - Siga os passos em [Baixar um VHD](../virtual-machines/windows/download-vhd.md).

    - Utilize o seguinte `sysprep` comando em vez do descrito no procedimento anterior.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Também pode consultar a [visão geral do Sysprep (preparação do sistema).](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

Utilize este VHD para agora criar e implementar um VM no seu dispositivo Azure Stack Edge.

## <a name="create-a-linux-custom-vm-image"></a>Crie uma imagem VM personalizada do Linux

Faça os seguintes passos para criar uma imagem Linux VM.

1. Crie uma Máquina Virtual do Linux. Para mais informações, vá ao Tutorial: Crie e gere os [VMs Linux com o Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

2. [Descarregue o disco DE existente.](../virtual-machines/linux/download-vhd.md)

Utilize este VHD para agora criar e implementar um VM no seu dispositivo Azure Stack Edge. Pode utilizar as seguintes duas imagens do Azure Marketplace para criar imagens personalizadas do Linux:

|Nome do item  |Descrição  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |O Ubuntu Server é o Linux mais popular do mundo para ambientes em nuvem.|Canónico|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux é uma das distribuições linux mais populares.     |credativ|

Para obter uma lista completa de imagens do mercado Azure que podem funcionar (atualmente não [testadas), aceda aos itens do Azure Marketplace disponíveis para o Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Passos seguintes

[Coloque VMs no seu dispositivo Azure Stack Edge](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
