---
title: Remover VMs do Azure Automation Change Tracking and Inventory
description: Este artigo diz como remover VMs de Change Tracking and Inventory.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84169459"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Remover VMs do Controlo de Alterações e Inventário

Quando terminar de implementar alterações nos VMs no seu ambiente, pode removê-las da função De rastreio e inventário de [alterações.](change-tracking.md)

## <a name="to-remove-your-vms"></a>Para remover os seus VMs

1. A partir da sua conta Demômes, selecione **Alterar rastreio** ou **Inventário** em **Gestão de Configuração.**

2. Utilize o seguinte comando para identificar o UUID de um VM que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No seu espaço de trabalho Log Analytics em **Geral,** aceda às pesquisas guardadas para a configuração do âmbito `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. Para a pesquisa `MicrosoftDefaultComputerGroup` guardada, clique na elipse para a direita e **selecione Editar**. 

5. Retire o UUID para o VM.

6. Repita os passos para que quaisquer outros VMs removam.

7. Guarde a procura guardada quando terminar de editá-la. 

## <a name="next-steps"></a>Passos seguintes

* Para continuar a trabalhar com change tracking and inventory, consulte [Gerir o Tracking e o Inventário de Mudanças.](change-tracking-file-contents.md)
* Para resolver problemas gerais de funcionalidade, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](troubleshoot/change-tracking.md).