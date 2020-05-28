---
title: Remover VMs da Azure Automation Update Management
description: Este artigo diz como remover VMs da Atualização.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 9bb9ee93be4e045b52355255ecb86e54e48e5c5d
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117422"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs da Gestão de Atualizações

Quando terminar de implementar atualizações para VMs no seu ambiente, pode removê-las da funcionalidade ['Gestão](automation-update-management.md) de Actualizações'.

1. Na sua conta Automation, selecione **Gestão de Atualização** sob gestão de **Atualização**.

2. Utilize o seguinte comando para identificar o UUID de um VM que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No seu espaço de trabalho Log Analytics em **Geral,** aceda às pesquisas guardadas para a configuração do âmbito `MicrosoftDefaultScopeConfig-Updates` .

4. Para obter a pesquisa `MicrosoftDefaultComputerGroup` guardada, clique na elipse à direita e **selecione Editar**. 

5. Retire o UUID para o VM.

6. Repita os passos para que quaisquer outros VMs removam.

7. Guarde a procura guardada quando terminar de editá-la. 

## <a name="next-steps"></a>Próximos passos

* Para continuar a trabalhar com a Atualização, consulte ['Gerir atualizações e patches' para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver problemas gerais de funcionalidade, consulte problemas de Gestão de Atualização de Resolução de [Problemas](troubleshoot/update-management.md).
* Para problemas com o agente de atualização do Windows, consulte problemas de agente de [atualização do Windows](troubleshoot/update-agent-issues.md).
* Para problemas com o agente de atualização Linux, consulte problemas de agente de [atualização Da Troubleshoot Linux](troubleshoot/update-agent-issues-linux.md).