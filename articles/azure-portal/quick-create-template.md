---
title: Crie um painel de porta-porta Azure utilizando um modelo de Gestor de Recursos Azure
description: Aprenda a criar um painel de instrumentos do portal Azure utilizando um modelo de Gestor de Recursos Azure.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/15/2021
ms.openlocfilehash: a3ab8767e09256ed8235dbd980ea3336a6f0fb1d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658334"
---
# <a name="quickstart-create-a-dashboard-in-the-azure-portal-by-using-an-arm-template"></a>Quickstart: Criar um dashboard no portal Azure utilizando um modelo ARM

Um dashboard no portal Azure é uma visão focada e organizada dos seus recursos em nuvem. Este quickstart foca-se no processo de implantação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar um dashboard. O painel de instrumentos mostra o desempenho de uma máquina virtual (VM), bem como algumas informações estáticas e ligações.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um VM existente.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

O painel de instrumentos que cria na parte seguinte deste quickstart requer um VM existente. Crie um VM seguindo estes passos.

1. No portal Azure, selecione **Cloud Shell**.

    ![Selecione a casca de nuvem da fita do portal Azure](media/quick-create-template/cloud-shell.png)

1. Na janela **Cloud Shell,** selecione **PowerShell**.

    ![Selecione PowerShell na janela do terminal](media/quick-create-template/powershell.png)

1. Copie o seguinte comando e introduza-o na solicitação de comando para criar um grupo de recursos.

    ```powershell
    New-AzResourceGroup -Name SimpleWinVmResourceGroup -Location EastUS
    ```

    ![Copie um comando para a solicitação de comando](media/quick-create-template/command-prompt.png)

1. Copie o seguinte comando e introduza-o na solicitação de comando para criar um VM no grupo de recursos.

    ```powershell
    New-AzVm `
        -ResourceGroupName "SimpleWinVmResourceGroup" `
        -Name "SimpleWinVm" `
        -Location "East US" 
    ```

1. Introduza um nome de utilizador e senha para o VM. Este é um novo nome de utilizador e senha; não é, por exemplo, a conta que usa para entrar no Azure. Para mais informações, consulte [os requisitos do nome de utilizador](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) e os [requisitos de senha.](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

    A implantação em VM começa agora e normalmente demora alguns minutos a ser concluída. Após a colocação concluída, passe para a secção seguinte.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-portal-dashboard/). O modelo para este artigo é muito longo para mostrar aqui. Para ver o modelo, consulte [azuredeploy.js.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-portal-dashboard/azuredeploy.json) Um recurso Azure é definido no modelo, [Microsoft.Portal/dashboards](/azure/templates/microsoft.portal/dashboards) - Crie um dashboard no portal Azure.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores e, em seguida, selecione **Rever + criar**.

    ![Modelo ARM, criar dashboard, implementar portal](media/quick-create-template/create-dashboard-using-template-portal.png)

    A menos que seja especificado, utilize os valores predefinidos para criar o painel de instrumentos.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **SimpleWinVmResourceGroup**.
    * **Localização**: selecione **East US**.
    * **Nome da máquina virtual**: insira **SimpleWinVm**.
    * **Grupo de Recursos de Máquina Virtual:** insira **o SimpleWinVmResourceGroup**.

1. Selecione **Criar** ou **Comprar**. Depois de o painel ter sido implantado com sucesso, recebe uma notificação:

    ![Modelo ARM, criar dashboard, implementar notificação do portal](media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

O portal Azure foi usado para implantar o modelo. Além do portal do Azure, também pode utilizar o Azure PowerShell, a CLI do Azure e a API REST. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender remover o VM e o painel de instrumentos associado, elimine o grupo de recursos que os contém.

1. No portal Azure, procure **o SimpleWinVmResourceGroup** e, em seguida, selecione-o nos resultados da pesquisa.

1. Na página **SimpleWinVmResourceGroup,** selecione **Delete resource group**, insira o nome do grupo de recursos para confirmar e, em seguida, selecione **Delete**.

    ![Eliminar grupo de recursos](media/quick-create-template/delete-resource-group.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os dashboards no portal Azure, consulte:

> [!div class="nextstepaction"]
> [Criar e partilhar dashboards no portal do Azure](azure-portal-dashboards.md)