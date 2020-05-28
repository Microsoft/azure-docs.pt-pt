---
title: Remover VMs do Rastreio e Inventário de Alterações de Automação Azure
description: Este artigo diz como remover VMs de Change Tracking e Inventory.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 23dfad844c17b0b8c8a35b6a94d6a96327afe19c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117442"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Remover VMs do Controlo de Alterações e Inventário

Quando terminar de implementar alterações em VMs no seu ambiente, pode removê-las da funcionalidade De rastreio e inventário de [alterações.](change-tracking.md)

1. Na sua conta de Automação, selecione **'Localizar rastreio** ou **Inventário'** sob gestão de **configuração**.

2. Utilize o seguinte comando para identificar o UUID de um VM que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No seu espaço de trabalho Log Analytics em **Geral,** aceda às pesquisas guardadas para a configuração do âmbito `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. Para obter a pesquisa `MicrosoftDefaultComputerGroup` guardada, clique na elipse à direita e **selecione Editar**. 

5. Retire o UUID para o VM.

6. Repita os passos para que quaisquer outros VMs removam.

7. Guarde a procura guardada quando terminar de editá-la. 

## <a name="next-steps"></a>Próximos passos

* Para continuar a trabalhar com o Change Tracking e Inventário, consulte [Manage Change Tracking and Inventory](change-tracking-file-contents.md).
* Para resolver problemas gerais de funcionalidades, consulte problemas de rastreio e inventário de alterações de resolução de [problemas.](troubleshoot/change-tracking.md)