---
title: Remover VMs do Rastreio e Inventário de Alterações de Automação Azure
description: Este artigo diz como remover VMs de Change Tracking e Inventory.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 22cb49c414e21e5c47330f2c67fc2cf30e3364b2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836673"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Remover VMs do Controlo de Alterações e Inventário

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

* Para continuar a trabalhar com o Change Tracking e Inventário, consulte [Manage Change Tracking and Inventory](change-tracking-file-contents.md).
* Para resolver problemas gerais de funcionalidades, consulte problemas de rastreio e inventário de alterações de resolução de [problemas.](troubleshoot/change-tracking.md)