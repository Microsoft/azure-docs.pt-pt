---
title: Crie um laboratório usando o modelo de Azure DevTest Labs e Gestor de Recursos
description: Neste tutorial, você cria um laboratório em Azure DevTest Labs usando um modelo de Gestor de Recursos Azure. Um administrador de laboratório cria um laboratório, cria VMs no laboratório, e configura políticas.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 22ba4ffeec22a45c07b096b0a754d08c8230dd8f
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85476211"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs-resource-manager-template"></a>Tutorial: Criar um laboratório utilizando a Azure DevTest Labs (modelo de gestor de recursos)
Neste tutorial, cria-se um laboratório com um VM do Datacenter 2019 do Windows Server 2019 utilizando um modelo de Gestor de Recursos Azure. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Rever o modelo 
> * Implementar o modelo
> * Verificar o modelo
> * Recursos de limpeza

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json" range="1-97" highlight="51-85":::

Os recursos definidos no modelo incluem:

- [**Microsoft.DevTestLab/laboratórios**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft.DevTestLab laboratórios/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Laboratórios microsoft.DevTestLab/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Para encontrar mais amostras de modelo, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Implementar o modelo
Para executar a implementação automaticamente, clique no botão seguinte. 

[![Implementar no Azure](./media/create-lab-windows-vm-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Crie um **novo grupo de recursos** para que seja fácil de limpar mais tarde.
1. Selecione uma **localização** para o grupo de recursos. 
1. Insira um **nome para o laboratório.** 
1. Insira um **nome para o VM**. 
1. Insira um **nome de utilizador** que possa aceder ao VM. 
1. Introduza a **palavra-passe** do utilizador. 
1. **Selecione Concordo com os termos e condições acima indicados.** 
1. Em seguida, **selecione Comprar**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Implementar página de modelo":::

## <a name="verify-the-deployment"></a>Verificar a implementação
1. Selecione **Notificações** no topo para ver o estado da implementação e clique **em Implementação no** link de progresso.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Notificação de implantação":::
2. Na página **'Implantação ' Overview',** aguarde até que a implantação esteja concluída. Esta operação (especialmente, criar um VM) leva algum tempo a ser concluída. Em seguida, selecione o seu **Grupo de Recursos para o grupo** de recursos ou o nome do grupo de **recursos,** como mostrado na seguinte imagem: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Navegue para o grupo de recursos":::
3. Na página do **grupo Recursos,** veja a lista de recursos no grupo de recursos. Confirme que vê o seu laboratório de tipo: `DevTest Lab` nos recursos. Você também vê os recursos dependentes, como a rede virtual e a máquina virtual no grupo de recursos. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Página inicial do grupo de recursos":::
4. Selecione o seu laboratório na lista de recursos para ver a página inicial do seu laboratório. Confirme que vê o VM do Datacenter 2019 do Windows Server na lista **das minhas máquinas virtuais.** Na imagem a seguir, a secção **Essencial** é minimizada. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Página inicial para o laboratório":::

    > [!IMPORTANT] 
    > Mantenha esta página aberta e siga as instruções na secção seguinte para limpar os recursos para evitar custos de funcionamento do laboratório e do VM em Azure. Se quiser passar pelo próximo tutorial para testar o acesso ao VM no laboratório, limpe os recursos depois de passar por aquele tutorial. 

## <a name="cleanup-resources"></a>Recursos de limpeza

1. Primeiro, apague o laboratório para que possa eliminar o grupo de recursos. Não poderá apagar o grupo de recursos com um laboratório. Para eliminar o laboratório, **selecione Delete** na barra de ferramentas. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Apagar botão de laboratório":::
 2. Na página de confirmação, digite o nome do **laboratório**e selecione **Delete**. 
 3. Espera até o laboratório ser apagado. Selecione o ícone da **campainha** para ver a notificação da operação de eliminação. Este processo leva algum tempo. Confirme a eliminação do laboratório e, em seguida, selecione o **grupo de recursos** no menu de migalhas de pão. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Confirmar a supressão de VM em notificações":::
 1. Na página do **grupo Recursos,** selecione Eliminar o grupo de **recursos** da barra de ferramentas. Na página de confirmação, digite o nome do **grupo de recursos**e selecione **Delete**. Verifique as notificações para confirmar que o grupo de recursos está eliminado.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Eliminar botão de grupo de recursos":::

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um laboratório com um VM. Para saber como aceder ao laboratório, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Aceder ao laboratório](tutorial-use-custom-lab.md)

