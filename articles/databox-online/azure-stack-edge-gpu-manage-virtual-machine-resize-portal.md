---
title: Redimensione VMs em Azure Stack Edge Pro GPU, Pro R, Mini R através do portal Azure
description: Aprenda a redimensionar as máquinas virtuais (VM) em execução no seu GPU Azure Stack Edge Pro, Azure Stack Edge Pro R, Azure Stack Edge Mini R através do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080517"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Utilize o portal Azure para redimensionar os VMs no seu GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo explica como redimensionar as máquinas virtuais (VMs) implantadas no seu dispositivo GPU Azure Stack Edge Pro.

       
## <a name="about-vm-sizing"></a>Sobre o tamanho de VM

O tamanho VM determina a quantidade de recursos computacional (como CPU, GPU e memória) que são disponibilizados ao VM. Deve criar máquinas virtuais utilizando um tamanho VM apropriado para a carga de trabalho da sua aplicação. 

Apesar de todas as máquinas estarem a funcionar no mesmo hardware, os tamanhos das máquinas têm limites diferentes para acesso ao disco. Isto pode ajudá-lo a gerir o acesso geral ao disco através dos seus VMs. Se uma carga de trabalho aumentar, também pode redimensionar uma máquina virtual existente.

Para obter mais informações, consulte [os tamanhos de VM suportados para o seu dispositivo.](azure-stack-edge-gpu-virtual-machine-sizes.md)


## <a name="prerequisites"></a>Pré-requisitos

Antes de redimensionar um VM em execução no seu dispositivo através do portal Azure, certifique-se de que:

1. Tem pelo menos um VM implantado no seu dispositivo. Para criar este VM, consulte as instruções em [Implementar VM no seu Azure Stack Edge Pro através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. O seu VM deve estar em **estado de paragem.** Para parar o seu VM, vá a **máquinas virtuais > visão geral** e selecione o VM que pretende parar. Na página 'Vista Geral', **selecione Stop** e, em seguida, selecione **Sim** quando solicitado para confirmação. Antes de redimensionar o seu VM, tem de parar o VM.

    ![Parar vM da página de visão geral](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Redimensionar uma VM

Siga estes passos para redimensionar uma máquina virtual implantada no seu dispositivo. 

1. Vá à máquina virtual que parou e, em seguida, vá para a página **geral.** Selecione **o tamanho VM (alteração)**.
    
    ![Selecione alterar tamanho VM na página de visão geral](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. Na lâmina de **tamanho Change VM,** a partir da barra de comando, selecione o **tamanho VM** e, em seguida, selecione **Alterar**.

    ![Selecione novo tamanho VM](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Verá uma notificação de que a máquina virtual está a ser atualizada. Depois de a máquina virtual ser atualizada com sucesso, a página **de visão geral** atualiza-se para exibir o VM redimensionado.

    ![VM redimensionado ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar máquinas virtuais no seu dispositivo Azure Stack Edge Pro, consulte [implementar máquinas virtuais através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
