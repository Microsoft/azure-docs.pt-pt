---
title: Reinicie um VM num laboratório em Azure DevTest Labs Microsoft Docs
description: Este artigo fornece passos para reiniciar de forma rápida e fácil as máquinas virtuais (VM) em Azure DevTest Labs.
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
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898186"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Reinicie um VM num laboratório em Azure DevTest Labs
Pode reiniciar de forma rápida e fácil uma máquina virtual em DevTest Labs seguindo os passos deste artigo. Considere o seguinte antes de reiniciar um VM:

- O VM deve estar a funcionar para que a função de reinício seja ativada.
- Se um utilizador estiver ligado a um VM em funcionamento quando efetuar um reinício, deve voltar a ligar-se ao VM depois de ter reajustado.
- Se um artefacto estiver a ser aplicado quando reiniciar o VM, recebe um aviso de que o artefacto pode não ser aplicado.

    ![Aviso ao reiniciar ao aplicar artefactos](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Se o VM tiver parado durante a aplicação de um artefacto, pode utilizar a função VM de reinício como uma forma potencial de resolver o problema.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Passos para reiniciar um VM num laboratório em Azure DevTest Labs
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório que inclui o VM que pretende reiniciar.
1. No painel esquerdo, selecione **As Minhas Máquinas Virtuais.**
1. Na lista de VMs, selecione um VM em execução.
1. No topo do painel de gestão VM, **selecione Restart**.

    ![Reiniciar botão VM](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorize o estado do reinício selecionando o ícone **de Notificações** no canto superior direito da janela.

    ![Visualização do estado do reinício do VM](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Também pode reiniciar um VM em execução selecionando a sua elipse (...) na lista das **Minhas Máquinas Virtuais.**

![Reiniciar VM através de elipses](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Passos seguintes
* Uma vez reiniciado, pode voltar a ligar-se ao VM selecionando **Connect** no painel de gestão.
* Explore a [galeria de modelos QuickStart do DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
