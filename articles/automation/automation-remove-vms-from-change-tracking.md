---
title: Remover VMs do Rastreio e Inventário de Alterações de Automação Azure
description: Este artigo diz como remover VMs de Change Tracking e Inventory.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 1bd94beaa40f6ff793b50e261138cc31453f7016
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749166"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Remover VMs do Rastreio e Inventário de Alterações

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

Quando terminar de implementar alterações em VMs no seu ambiente, pode removê-las da funcionalidade De rastreio e inventário de [alterações.](change-tracking.md)

1. Na sua conta de Automação, selecione **'Localizar rastreio** ou **Inventário'** sob gestão de **configuração**.

2. Utilize o seguinte comando para identificar o UUID de um VM que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No seu espaço de trabalho Log Analytics em **Geral,** aceda às pesquisas guardadas.

4. Para obter a pesquisa `MicrosoftDefaultComputerGroup` guardada, clique na elipse à direita e **selecione Editar**. 

5. Retire o UUID para o VM.

6. Repita os passos para que quaisquer outros VMs removam.

7. Guarde a procura guardada quando terminar de editá-la. 

## <a name="next-steps"></a>Passos seguintes

* [Alterar visão geral do rastreio e do inventário](change-tracking.md)
* [Gerir o rastreio e o inventário de alterações](change-tracking-file-contents.md)
* [Desvincular o espaço de trabalho da conta da Automação para rastreio de alterações e inventário](automation-unlink-workspace-change-tracking.md)
* [Ativar o Rastreio de Alterações e O Inventário a partir de uma conta de Automação](automation-enable-changes-from-auto-acct.md)
* [Ativar o rastreio e o inventário de alterações do portal Azure](automation-enable-changes-from-browse.md)
* [Ativar o rastreio de alterações e o inventário a partir de um livro de execução](automation-enable-changes-from-runbook.md)
* [Ativar o rastreio e o inventário de alterações de um VM Azure](automation-enable-changes-from-vm.md)
* [Mudanças de resolução de problemas num VM Azure](automation-tutorial-troubleshoot-changes.md)
* [Problemas de mudança de mudança e problemas de inventário](troubleshoot/change-tracking.md)