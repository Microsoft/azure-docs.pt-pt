---
title: Remover VMs da Azure Automation Update Management
description: Este artigo diz como remover VMs da Atualização.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836656"
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

* Para continuar a trabalhar com a Atualização, consulte ['Gerir atualizações e patches' para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver problemas gerais de funcionalidade, consulte problemas de Gestão de Atualização de Resolução de [Problemas](troubleshoot/update-management.md).
* Para problemas com o agente de atualização do Windows, consulte problemas de agente de [atualização do Windows](troubleshoot/update-agent-issues.md).
* Para problemas com o agente de atualização Linux, consulte problemas de agente de [atualização Da Troubleshoot Linux](troubleshoot/update-agent-issues-linux.md).