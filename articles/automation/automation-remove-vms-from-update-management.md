---
title: Remover VMs da Gestão de Azure Automation Update Management
description: Este artigo diz como remover VMs da Gestão de Atualização.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 796cf18ae4dbab50eb7f968bda065ae0351f2ae8
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84169411"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs da Gestão de Atualizações

Quando terminar de implementar atualizações para VMs no seu ambiente, pode removê-las da função De Gestão de [Atualização.](automation-update-management.md)

## <a name="to-remove-your-vms"></a>Para remover os seus VMs

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. Utilize o seguinte comando para identificar o UUID de um VM que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No seu espaço de trabalho Log Analytics em **Geral,** aceda às pesquisas guardadas para a configuração do âmbito `MicrosoftDefaultScopeConfig-Updates` .

4. Para a pesquisa `MicrosoftDefaultComputerGroup` guardada, clique na elipse para a direita e **selecione Editar**. 

5. Retire o UUID para o VM.

6. Repita os passos para que quaisquer outros VMs removam.

7. Guarde a procura guardada quando terminar de editá-la. 

## <a name="next-steps"></a>Próximos passos

* Para continuar a trabalhar com a Gestão de Atualização, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver problemas gerais de funcionalidade, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para problemas com o agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de resolução do Troubleshoot .
* Para problemas com o agente de atualização Linux, consulte [problemas de agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md)de resolução de resolução de resolução de resolução de problemas .