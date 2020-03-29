---
title: Reimplantar um VM num laboratório em Azure DevTest Labs [ Microsoft Docs
description: Aprenda a reimplantar uma máquina virtual (mover-se de um nó Azure para outro) em Azure DevTest Labs.
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
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561629"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Reimplantar um VM num laboratório em Laboratórios Azure DevTest
Se não conseguir ligar-se a uma máquina virtual (VM) num laboratório através de uma ligação remota de ambiente de trabalho, reimplante o VM e tente ligar-se novamente a ele. Quando reimplanta um VM, o DevTest Labs move o VM do nó em que está a correr para um novo nó dentro da infraestrutura Azure. Em seguida, inicia o VM, mantendo todas as suas opções de configuração e recursos associados. Esta funcionalidade poupa-lhe o tempo gasto em resolver problemas na sua ligação remota de ambiente de trabalho ou no acesso à aplicação a VMs baseados no Windows no laboratório. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Passos para reimplantar um VM em um laboratório 
Para reimplantar um VM num laboratório em Azure DevTest Labs, tome os seguintes passos: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório que inclui o VM que quer reimplantar.  
4. No painel esquerdo, selecione **My Virtual Machines**. 
5. A partir da lista de VMs, selecione um VM.
6. Na página Máquina Virtual para o seu VM, selecione **Recolocar** em **OPERAÇÕES** no menu esquerdo.

    ![Voltar a implementar](media/devtest-lab-redeploy-vm/redeploy.png)
7. Leia as informações na página e selecione **botão Recolocar.** 9. Verifique o estado da operação de reimplantação na janela **Notificações.**

    ![Estado de reimplantação](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Passos seguintes
Aprenda a redimensionar um VM em Azure DevTest Labs, consulte [Resize a VM](devtest-lab-resize-vm.md).


