---
title: Recolocar um VM num laboratório em Azure DevTest Labs Microsoft Docs
description: Aprenda a recolocar uma máquina virtual (passe de um nó Azure para outro) em Azure DevTest Labs.
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
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898725"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Recolocar um VM num laboratório em Azure DevTest Labs
Se não conseguir ligar-se a uma máquina virtual (VM) num laboratório através de uma ligação remota de ambiente de trabalho, relofectu o VM e tente ligá-lo novamente. Quando se recoloca um VM, a DevTest Labs move o VM do nó em que está a correr para um novo nó dentro da infraestrutura Azure. Em seguida, inicia o VM mantendo todas as suas opções de configuração e recursos associados. Esta funcionalidade poupa-lhe o tempo gasto na resolução de problemas da sua ligação remota ao ambiente de trabalho ou acesso a VMs baseados no Windows no laboratório. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Passos para recolocar um VM em laboratório 
Para recolocar um VM num laboratório em Azure DevTest Labs, tome os seguintes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório que inclui o VM que pretende recolocar.  
4. No painel esquerdo, selecione **As Minhas Máquinas Virtuais.** 
5. Na lista de VMs, selecione um VM.
6. Na página Máquina Virtual para o seu VM, selecione **Reposicionar-se** em **OPERAções** no menu esquerdo.

    ![Voltar a implementar](media/devtest-lab-redeploy-vm/redeploy.png)
7. Leia as informações na página e selecione o botão **De reafectação.** 9. Verifique o estado da operação de recolocação na janela **Notificações.**

    ![Estado de reimplantação](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Passos seguintes
Aprenda a redimensionar um VM em Azure DevTest Labs, consulte [Redimensionar um VM](devtest-lab-resize-vm.md).


