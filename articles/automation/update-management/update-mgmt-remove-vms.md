---
title: Remover VMs da Gestão de Azure Automation Update Management
description: Este artigo diz como remover máquinas geridas com a Gestão de Atualização.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450442"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs da Gestão de Atualizações

Quando terminar de gerir atualizações sobre os seus VMs no seu ambiente, pode parar de gerir VMs com a funcionalidade De Gestão de [Atualização.](update-mgmt-overview.md)

## <a name="sign-into-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

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

## <a name="next-steps"></a>Passos seguintes

Para voltar a ativar a sua máquina virtual, consulte [Ativar a Gestão de Atualização navegando no portal Azure](update-mgmt-enable-portal.md) ou ativando a [Gestão de Atualização a partir de um VM Azure](update-mgmt-enable-vm.md).