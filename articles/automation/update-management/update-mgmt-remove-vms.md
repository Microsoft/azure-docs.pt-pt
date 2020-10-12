---
title: Remover VMs da Gestão de Azure Automation Update Management
description: Este artigo diz como remover máquinas geridas com a Gestão de Atualização.
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89648640"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs da Gestão de Atualizações

Quando terminar de gerir atualizações sobre os seus VMs no seu ambiente, pode parar de gerir VMs com a funcionalidade De Gestão de [Atualização.](update-mgmt-overview.md) Para deixar de os gerir, irá editar a consulta de pesquisa guardada `MicrosoftDefaultComputerGroup` no seu espaço de trabalho Log Analytics que está ligado à sua conta Automation.

## <a name="sign-into-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Para remover os seus VMs

1. No portal Azure, lance **a Cloud Shell** a partir da navegação superior do portal Azure. Se não estiver familiarizado com a Azure Cloud Shell, consulte [a visão geral da Azure Cloud Shell.](../../cloud-shell/overview.md)

2. Utilize o seguinte comando para identificar o UUID de uma máquina que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No portal Azure, navegue para **log analytics espaços de trabalho**. Selecione o seu espaço de trabalho da lista.

4. No seu espaço de trabalho Log Analytics, selecione **Logs** e, em seguida, e escolha **o explorador de consulta** no menu de ações de topo.

5. A partir do explorador de **consultas** no painel direito, expanda as **Queries Salvas\Updates** e selecione a consulta de pesquisa guardada `MicrosoftDefaultComputerGroup` para editá-la.

6. No editor de consulta, reveja a consulta e encontre o UUID para o VM. Retire o UUID para o VM e repita os passos para quaisquer outros VMs que pretenda remover.

7. Guarde a procura guardada quando terminar de editá-la selecionando **Save** a partir da barra superior.

>[!NOTE]
>As máquinas ainda são mostradas depois de as ter desenrolado porque reportamos todas as máquinas avaliadas nas últimas 24 horas. Depois de retirar a máquina, tem de esperar 24 horas antes de deixarem de estar listadas.

## <a name="next-steps"></a>Passos seguintes

Para voltar a ativar a sua máquina virtual, consulte [Ativar a Gestão de Atualização navegando no portal Azure](update-mgmt-enable-portal.md) ou ativando a [Gestão de Atualização a partir de um VM Azure](update-mgmt-enable-vm.md).