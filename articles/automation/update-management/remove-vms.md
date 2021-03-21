---
title: Remover VMs da Gestão de Azure Automation Update Management
description: Este artigo diz como remover máquinas geridas com a Gestão de Atualização.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97913197"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs da Gestão de Atualizações

Quando terminar de gerir atualizações sobre os seus VMs no seu ambiente, pode parar de gerir VMs com a funcionalidade De Gestão de [Atualização.](overview.md) Para deixar de os gerir, irá editar a consulta de pesquisa guardada `MicrosoftDefaultComputerGroup` no seu espaço de trabalho Log Analytics que está ligado à sua conta Automation.

## <a name="sign-into-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Para remover os seus VMs

1. No portal Azure, lance **a Cloud Shell** a partir da navegação superior do portal Azure. Se não estiver familiarizado com a Azure Cloud Shell, consulte [a visão geral da Azure Cloud Shell.](../../cloud-shell/overview.md)

2. Utilize o seguinte comando para identificar o UUID de uma máquina que pretende remover da gestão.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. No portal Azure, navegue para **log analytics espaços de trabalho**. Selecione o seu espaço de trabalho da lista.

4. No seu espaço de trabalho Log Analytics, selecione **Definições Avançadas** e, em seguida, escolha **Grupos** de Computador no menu da mão esquerda.

5. De **Grupos de Computador** no painel direito, selecione **Grupos Salvos**.

6. A partir da tabela, para a consulta de pesquisa guardada **Updates:MicrosoftDefaultComputerGroup**, clique no ícone **'Ver' para** executar e ver os seus membros.

7. No editor de consulta, reveja a consulta e encontre o UUID para o VM. Retire o UUID para o VM e repita os passos para quaisquer outros VMs que pretenda remover.

8. Guarde a procura guardada quando terminar de editá-la selecionando **Save** a partir da barra superior. Quando solicitado, especifique o seguinte:

    * **Nome**: MicrosoftDefaultComputerGroup
    * **Guardar como**: Função
    * **Pseudónimo :** Updates__MicrosoftDefaultComputerGroup
    * **Categoria**: Atualizações

>[!NOTE]
>As máquinas ainda são mostradas depois de as ter desenrolado porque reportamos todas as máquinas avaliadas nas últimas 24 horas. Depois de retirar a máquina, tem de esperar 24 horas antes de deixarem de estar listadas.

## <a name="next-steps"></a>Passos seguintes

Para voltar a ativar a sua máquina virtual, consulte [Ativar a Gestão de Atualização navegando no portal Azure](enable-from-portal.md) ou ativando a [Gestão de Atualização a partir de um VM Azure](enable-from-vm.md).