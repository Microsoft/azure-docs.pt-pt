---
title: Recolocar um VM num laboratório em Azure DevTest Labs Microsoft Docs
description: Aprenda a recolocar uma máquina virtual (passe de um nó Azure para outro) em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da0edf13adaa0d7ecd84ee2c190f376c19b398db
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85480240"
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


