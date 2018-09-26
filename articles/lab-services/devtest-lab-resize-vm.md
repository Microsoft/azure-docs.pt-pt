---
title: Redimensionar uma VM num laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como redimensionar uma máquina virtual no Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 9b9a1839bf4b028aec13b764b4de66385de4189e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47090756"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Redimensionar uma VM num laboratório no Azure DevTest Labs
Um dos recursos importantes de máquinas virtuais do Azure é o que permite-lhe alterar o tamanho de uma máquina virtual (VM) com base nas suas necessidades de CPU, rede ou o desempenho do disco. O Azure DevTest Labs suporta esta funcionalidade para VMs num laboratório agora. A funcionalidade de redimensionamento cumpra a política do laboratório para tamanhos de VM permitidos no laboratório. Ou seja, pode alterar o tamanho de uma VM para tamanhos permitidos apenas no laboratório. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Passos para redimensionar uma VM num laboratório 
Redimensionar uma VM num laboratório no Azure DevTest Labs, siga os passos seguintes: 

> [!NOTE]
> Se estiver ligado à VM através de uma sessão de área de trabalho remota (RDP), guarde o seu trabalho e desligue da VM antes de redimensioná-lo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório que inclui a VM que deseja redimensionar.  
4. No painel esquerdo, selecione **minhas máquinas de virtuais**. 
5. Na lista de VMs, selecione uma VM.
6. Selecione **parar** na barra de ferramentas se a VM está em execução. Verificar o estado da operação na **notificações** janela. Aguarde até que a VM está parado e, em seguida, feche o **notificações** janela. 

    ![Parar a VM](media/devtest-lab-resize-vm/stop-vm.png)
1. Na página da Máquina Virtual para a sua VM, selecione **tamanho** sob **definições** no menu à esquerda.

    ![Menu de tamanho](media/devtest-lab-resize-vm/size-menu.png)
1. Na **escolher um tamanho** janela, procurar e selecionar um tamanho para a sua VM e clique em **selecione**.     
1. Verificar o estado da operação de redimensionamento na **notificações** janela.

    ![Redimensionar o Estado](media/devtest-lab-resize-vm/resize-status.png)
10. Depois do redimensionamento operação for concluída com êxito, feche o **notificações** janela. 
11. Selecione **descrição geral** no menu à esquerda e selecione **reiniciar** na barra de ferramentas para reiniciar a VM. 

## <a name="next-steps"></a>Passos Seguintes
Para obter informações detalhadas sobre a funcionalidade de redimensionamento suportada por máquinas virtuais do Azure, consulte [redimensionar máquinas virtuais](https://azure.microsoft.com/blog/resize-virtual-machines/).


