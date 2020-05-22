---
title: Remover VMs da Azure Automation Update Management
description: Este artigo diz como remover VMs da Atualização.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 29d6edd5463de78bba039e4ed6219575d924ac10
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749145"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs da Gestão de Atualizações

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

Quando terminar de implementar atualizações para VMs no seu ambiente, pode removê-las da funcionalidade ['Gestão](automation-update-management.md) de Actualizações'.

1. Na sua conta Automation, selecione **Gestão de Atualização** sob gestão de **Atualização**.

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

* [Gerencie atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md)
* [Desvincular espaço de trabalho da conta automation para gestão de atualizações](automation-unlink-workspace-update-management.md)
* [Ativar gestão de atualização a partir de uma conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Ativar gestão de atualização a partir do portal Azure](automation-onboard-solutions-from-browse.md)
* [Ativar gestão de atualizações a partir de um livro de execução](automation-onboard-solutions.md)
* [Ativar a Gestão de Atualizações a partir de um Azure VM](automation-onboard-solutions-from-vm.md)
* [Problemas problemas de Gestão de Atualização de Problemas](troubleshoot/update-management.md)
* [Problemas problemas com problemas com o agente de atualização do Windows](troubleshoot/update-agent-issues.md)
* [Problemas problemas com problemas de problemas com problemas de problemas com problemas de agente de atualização linux](troubleshoot/update-agent-issues-linux.md)
