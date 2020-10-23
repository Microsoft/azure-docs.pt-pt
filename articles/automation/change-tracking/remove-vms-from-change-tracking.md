---
title: Remover VMs do Azure Automation Change Tracking and Inventory
description: Este artigo diz como remover VMs de Change Tracking and Inventory.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 9a7f36299d235ca2578a1ff3518650c5632ef046
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210260"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Remover VMs do Controlo de Alterações e Inventário

Quando terminar de rastrear as alterações nos VMs no seu ambiente, pode parar de geri-las com a funcionalidade [De Rastreio e Inventário de Alterações.](overview.md) Para deixar de os gerir, irá editar a consulta de pesquisa guardada `MicrosoftDefaultComputerGroup` no seu espaço de trabalho Log Analytics que está ligado à sua conta Automation.

## <a name="sign-into-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Para remover os seus VMs

1. No portal Azure, lance **a Cloud Shell** a partir da navegação superior do portal Azure. Se não estiver familiarizado com a Azure Cloud Shell, consulte [a visão geral da Azure Cloud Shell.](/cloud-shell/overview)

2. Utilize o seguinte comando para identificar o UUID de uma máquina que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No portal Azure, navegue para **log analytics espaços de trabalho**. Selecione o seu espaço de trabalho da lista.

4. No seu espaço de trabalho Log Analytics, selecione **Logs** e, em seguida, e escolha **o explorador de consulta** no menu de ações de topo.

5. A partir do explorador de **consultas** no painel direito, expanda as **Queries Salvas\Updates** e selecione a consulta de pesquisa guardada `MicrosoftDefaultComputerGroup` para editá-la.

6. No editor de consulta, reveja a consulta e encontre o UUID para o VM. Retire o UUID para o VM e repita os passos para quaisquer outros VMs que pretenda remover.

7. Guarde a procura guardada quando terminar de editá-la selecionando **Save** a partir da barra superior.

>[!NOTE]
>As máquinas ainda são mostradas depois de as ter desenrolado porque reportamos todas as máquinas avaliadas nas últimas 24 horas. Depois de retirar a máquina, tem de esperar 24 horas antes de deixarem de estar listadas.

## <a name="next-steps"></a>Passos seguintes

Para reequipar esta funcionalidade, consulte [Ativar o Rastreio e Inventário de Mudança a partir de uma conta de Automação](enable-from-automation-account.md), [Ativar o Tracking e o Inventário de Alterações navegando no portal Azure](enable-from-portal.md), Ativar o [Rastreio de Alterações e O Inventário a partir de um runbook](enable-from-runbook.md), ou [ativar o Tracking e Inventário de Mudança a partir de um VM Azure](enable-from-vm.md).