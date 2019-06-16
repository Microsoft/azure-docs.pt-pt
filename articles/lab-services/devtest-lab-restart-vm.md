---
title: Reiniciar uma VM num laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como reiniciar uma máquina virtual no Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 34c08a79abf6acb5ae8582ecd0743a890d850fc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60864426"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Reiniciar uma VM num laboratório no Azure DevTest Labs
Pode rapidamente e facilmente reiniciar uma máquina virtual no DevTest Labs, seguindo os passos neste artigo. Considere o seguinte antes de reiniciar uma VM:

- Tem de executar a VM para a funcionalidade de reinício seja ativado.
- Se um utilizador estiver ligado a uma VM em execução ao executar um reinício, eles devem voltar a ligar à VM depois de iniciar a cópia de segurança.
- Se um artefato está a ser aplicado ao reiniciar a VM, receberá um aviso de que o artefacto poderá não ser aplicado.

    ![Aviso ao reiniciar ao aplicar artefactos](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Se a VM tenha sido paralisado ao aplicar um artefato, pode utilizar a funcionalidade VM de reinício como uma possível maneira de resolver o problema.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Passos para reiniciar uma VM num laboratório no Azure DevTest Labs
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório que inclui a VM que pretende reiniciar.
1. No painel esquerdo, selecione **minhas máquinas de virtuais**.
1. Na lista de VMs, selecione uma VM em execução.
1. Na parte superior do painel de gestão da VM, selecione **reiniciar**.

    ![Reinicie o botão VM](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorizar o estado do reinício, selecionando o **notificações** na parte superior direita da janela.

    ![Visualizar o estado relativamente ao reinício VM](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Também pode reiniciar uma VM em execução ao selecionar o botão de reticências (...) na lista de **minhas máquinas de virtuais**.

![Reinicie a VM através de reticências](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Passos Seguintes
* Depois de ser reiniciado, pode voltar a ligar à VM selecionando **Connect** sobre o seu painel de gestão.
* Explorar o [Galeria de modelos de início rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
