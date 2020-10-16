---
title: Redimensione um VM em um laboratório em Azure DevTest Labs
description: Saiba como alterar o tamanho de uma máquina virtual (VM) em Azure DevTest Labs com base nas suas necessidades de CPU, rede ou desempenho em disco.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 592be0862bc33ead86fe43d37753fb72fde120c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482025"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Redimensione um VM em um laboratório em Azure DevTest Labs
Uma das características importantes das máquinas virtuais Azure é que permite alterar o tamanho de uma máquina virtual (VM) com base nas suas necessidades de CPU, rede ou desempenho em disco. A Azure DevTest Labs suporta esta funcionalidade para VMs num laboratório agora. O recurso de redimensionar adere à política do laboratório para tamanhos de VM permitidos no laboratório. Ou seja, podes mudar o tamanho de um VM para tamanhos apenas permitidos no laboratório. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Passos para redimensionar um VM em um laboratório 
Para redimensionar um VM num laboratório em Azure DevTest Labs, tome os seguintes passos: 

> [!NOTE]
> Se estiver ligado ao VM através de uma sessão de ambiente de trabalho remoto (RDP), guarde o seu trabalho e desligue-o do VM antes de o redimensionar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório que inclui o VM que pretende redimensionar.  
4. No painel esquerdo, selecione **As Minhas Máquinas Virtuais.** 
5. Na lista de VMs, selecione um VM.
6. **Selecione Parar** na barra de ferramentas se o VM estiver em funcionamento. Verifique o estado da operação na janela **Notificações.** Aguarde até que o VM seja interrompido e, em seguida, feche a janela **notificações.** 

    ![Parar a VM](media/devtest-lab-resize-vm/stop-vm.png)
1. Na página 'Máquina Virtual' para o seu VM, selecione **Tamanho** em **DEFINIÇÕES** no menu esquerdo.

    ![Menu de tamanho](media/devtest-lab-resize-vm/size-menu.png)
1. Na janela **de tamanho,** navegue e selecione um tamanho para o seu VM e clique em **Select**.     
1. Verifique o estado da operação de redimensionar na janela **Notificações.**

    ![Redimensionar o estado](media/devtest-lab-resize-vm/resize-status.png)
10. Após o redimensionar a operação, feche a janela **Notificações.** 
11. Selecione **a visão geral** no menu esquerdo e selecione **Reiniciar** na barra de ferramentas para reiniciar o VM. 

## <a name="next-steps"></a>Passos seguintes
Para obter informações detalhadas sobre a funcionalidade de redimensionação suportada por máquinas virtuais Azure, consulte [redimensionar máquinas virtuais](https://azure.microsoft.com/blog/resize-virtual-machines/).


