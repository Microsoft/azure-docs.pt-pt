---
title: Remover VMs da Gestão de Azure Automation Update Management
description: Este artigo diz como remover máquinas geridas com a Gestão de Atualização.
services: automation
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: mvc
ms.openlocfilehash: 9745ddea1035f239a9ca65a073fb698a8f42c01f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610052"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs da Gestão de Atualizações

Quando terminar de implementar atualizações para VMs no seu ambiente, pode removê-las da função De Gestão de [Atualização.](automation-update-management.md)

## <a name="to-remove-your-vms"></a>Para remover os seus VMs

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. Utilize o seguinte comando para identificar o UUID de uma máquina que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No seu espaço de trabalho Log Analytics em **Geral,** aceda às pesquisas guardadas para a configuração do âmbito `MicrosoftDefaultScopeConfig-Updates` .

4. Para a pesquisa `MicrosoftDefaultComputerGroup` guardada, clique na elipse para a direita e **selecione Editar**.

5. Retire o UUID para o VM.

6. Repita os passos para que quaisquer outros VMs removam.

7. Guarde a procura guardada quando terminar de editá-la.

>[!NOTE]
>As máquinas ainda são mostradas depois de as ter desenrolado porque reportamos todas as máquinas avaliadas nas últimas 24 horas. Depois de desligar a máquina, é necessário esperar 24 horas antes de deixarem de estar listadas.

## <a name="next-steps"></a>Próximos passos

* Para continuar a trabalhar com a Gestão de Atualização, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver problemas gerais de funcionalidade, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para problemas com o agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de resolução do Troubleshoot .
* Para problemas com o agente de atualização Linux, consulte [problemas de agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md)de resolução de resolução de resolução de resolução de problemas .