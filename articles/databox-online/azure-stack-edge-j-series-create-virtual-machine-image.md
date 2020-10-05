---
title: Crie imagens VM para o seu dispositivo GPU Azure Stack Edge Pro
description: Descreve como criar imagens linux ou Windows VM para usar com o seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/04/2020
ms.author: alkohli
ms.openlocfilehash: 745d0df07b6d0d01acf0d564df8c242e16f3f56d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90890994"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Crie imagens VM personalizadas para o seu dispositivo Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Para implementar VMs no seu dispositivo Azure Stack Edge Pro, precisa de ser capaz de criar imagens VM personalizadas que pode utilizar para criar VMs. Este artigo descreve os passos necessários para criar imagens personalizadas do Linux ou Windows VM que pode utilizar para implantar VMs no seu dispositivo Azure Stack Edge Pro.

## <a name="vm-image-workflow"></a>Fluxo de trabalho de imagem VM

O fluxo de trabalho requer que você crie uma máquina virtual em Azure, personalize o VM, generalize e, em seguida, descarregue o VHD correspondente ao VM. Este VHD generalizado é carregado para Azure Stack Edge Pro, o disco gerido é criado a partir desse VHD, a imagem é criada a partir de disco gerido, e finalmente os VMs são criados a partir dessa imagem.   

Para obter mais informações, aceda a [implementar um VM no seu dispositivo Azure Stack Edge Pro utilizando a Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Crie uma imagem VM personalizada do Windows

Faça os seguintes passos para criar uma imagem VM do Windows.

1. Criar uma máquina virtual windows. Para mais informações, vá ao [Tutorial: Crie e gere vMs do Windows com a Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Descarregue um disco operativo existente.

    - Siga os passos em [Baixar um VHD](../virtual-machines/windows/download-vhd.md).

    - Utilize o seguinte `sysprep` comando em vez do descrito no procedimento anterior.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Também pode consultar a [visão geral do Sysprep (preparação do sistema).](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

Utilize este VHD para agora criar e implementar um VM no seu dispositivo Azure Stack Edge Pro.

## <a name="create-a-linux-custom-vm-image"></a>Crie uma imagem VM personalizada do Linux

Faça os seguintes passos para criar uma imagem Linux VM.

1. Crie uma Máquina Virtual do Linux. Para mais informações, vá ao Tutorial: Crie e gere os [VMs Linux com o Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

1. Desaprovisione a VM. Utilize o agente Azure VM para eliminar ficheiros e dados específicos da máquina. Utilize o `waagent` comando com o parâmetro na sua fonte `-deprovision+user` Linux VM. Para obter mais informações, consulte [compreensão e utilizando o Agente Azure Linux](../virtual-machines/extensions/agent-linux.md).

    1. Ligue-se ao seu Linux VM com um cliente SSH.
    2. Na janela SSH, insira o seguinte comando:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Só executar este comando num VM que irá capturar como uma imagem. Este comando não garante que a imagem seja limpa de toda a informação sensível ou seja adequada para redistribuição. O `+user` parâmetro também remove a última conta de utilizador a provisionada. Para manter as credenciais de conta de utilizador no VM, utilize apenas `-deprovision` .
     
    3. Entre **para** continuar. Pode adicionar o `-force` parâmetro para evitar este passo de confirmação.
    4. Após o comando estar concluído, entre na **saída** para fechar o cliente SSH.  O VM ainda estará em execução neste momento.


1. [Descarregue o disco DE existente.](../virtual-machines/linux/download-vhd.md)

Utilize este VHD para agora criar e implementar um VM no seu dispositivo Azure Stack Edge Pro. Pode utilizar as seguintes duas imagens do Azure Marketplace para criar imagens personalizadas do Linux:

|Nome do item  |Descrição  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |O Ubuntu Server é o Linux mais popular do mundo para ambientes em nuvem.|Canónico|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux é uma das distribuições linux mais populares.     |credativ|

Para obter uma lista completa de imagens do Azure Marketplace que possam funcionar (atualmente não [testadas), aceda aos itens do Azure Marketplace disponíveis para o Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Passos seguintes

[Coloque VMs no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
