---
title: Redimensionar um VM em um laboratório em Azure DevTest Labs
description: Aprenda a alterar o tamanho de uma máquina virtual (VM) em Laboratórios Azure DevTest com base nas suas necessidades de CPU, rede ou desempenho em disco.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756182"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Redimensionar um VM em um laboratório em Azure DevTest Labs
Uma das características importantes das máquinas virtuais Azure é que permite alterar o tamanho de uma máquina virtual (VM) com base nas suas necessidades de CPU, rede ou desempenho em disco. A Azure DevTest Labs suporta esta funcionalidade para VMs num laboratório agora. A funcionalidade de redimensionar adere à política do laboratório para tamanhos de VM permitidos no laboratório. Ou seja, podes mudar o tamanho de um VM para apenas tamanhos permitidos no laboratório. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Passos para redimensionar um VM em um laboratório 
Para redimensionar um VM num laboratório em Azure DevTest Labs, tome os seguintes passos: 

> [!NOTE]
> Se estiver ligado ao VM através de uma sessão remota de ambiente de trabalho (RDP), guarde o seu trabalho e desligue-o do VM antes de o redimensionar.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório que inclui o VM que quer redimensionar.  
4. No painel esquerdo, selecione **My Virtual Machines**. 
5. A partir da lista de VMs, selecione um VM.
6. Selecione **Parar** na barra de ferramentas se o VM estiver em funcionamento. Verifique o estado da operação na janela **Notificações.** Aguarde até que o VM seja parado e feche a janela **notificações.** 

    ![Parar a VM](media/devtest-lab-resize-vm/stop-vm.png)
1. Na página Máquina Virtual para o seu VM, selecione **Tamanho** em **DEFINIÇÕES** no menu esquerdo.

    ![Menu tamanho](media/devtest-lab-resize-vm/size-menu.png)
1. Na janela Escolha uma janela **de tamanho,** navegue e selecione um tamanho para o seu VM e clique em **Selecionar**.     
1. Verifique o estado da operação de redimensionar na janela **Notificações.**

    ![Estatuto de redimensionado](media/devtest-lab-resize-vm/resize-status.png)
10. Depois de a operação de redimensionar o sucesso, feche a janela **Notificações.** 
11. Selecione **visão geral** no menu esquerdo e selecione **Reiniciar** na barra de ferramentas para reiniciar o VM. 

## <a name="next-steps"></a>Passos seguintes
Para obter informações detalhadas sobre a funcionalidade de redimensionar suportada por máquinas virtuais Azure, consulte [redimensionar máquinas virtuais](https://azure.microsoft.com/blog/resize-virtual-machines/).


