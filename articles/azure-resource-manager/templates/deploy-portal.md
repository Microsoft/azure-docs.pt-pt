---
title: Implementar recursos com o portal Azure
description: Utilize o portal Azure e o Azure Resource Manage para implementar os seus recursos num grupo de recursos na sua subscrição.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d8467bb4e51fc4e6ba89a84f1260a8d2743758d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028680"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Implementar recursos com modelos ARM e portal Azure

Saiba como utilizar o [portal Azure](https://portal.azure.com) com [modelos de Gestor de Recursos Azure (modelos ARM)](overview.md) para implementar os seus recursos Azure. Para saber sobre a gestão dos seus recursos, consulte [gerir os recursos do Azure utilizando o portal Azure.](../management/manage-resources-portal.md)

A implantação de recursos Azure através do portal Azure normalmente envolve dois passos:

- Crie um grupo de recursos.
- Mobilizar recursos para o grupo de recursos.

Além disso, pode criar um modelo ARM personalizado para implementar recursos Azure.

Este artigo mostra ambos os métodos.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Para criar um novo grupo de recursos, selecione **grupos** de recursos do [portal Azure](https://portal.azure.com).

   ![Selecione grupos de recursos](./media/deploy-portal/select-resource-groups.png)

1. Em grupos de recursos, **selecione Adicionar**.

   ![Adicionar grupo de recursos](./media/deploy-portal/add-resource-group.png)

1. Selecione ou introduza os seguintes valores de propriedade:

    - **Assinatura**: Selecione uma subscrição Azure.
    - **Grupo de recursos**: Dê um nome ao grupo de recursos.
    - **Região**: Especifique uma localização Azure. Este local é onde o grupo de recursos armazena metadados sobre os recursos. Por razões de conformidade, pode querer especificar onde esses metadados são armazenados. Em geral, recomendamos que especifique um local onde a maioria dos seus recursos estarão. Usar a mesma localização pode simplificar o seu modelo.

   ![Definir valores de grupo](./media/deploy-portal/set-group-properties.png)

1. Selecione **Rever + criar**.
1. Reveja os valores e, em seguida, **selecione Criar**.
1. Selecione **Refresh** antes de poder ver o novo grupo de recursos na lista.

## <a name="deploy-resources-to-a-resource-group"></a>Mobilizar recursos para um grupo de recursos

Depois de criar um grupo de recursos, pode mobilizar recursos para o grupo a partir do Marketplace. O Marketplace fornece soluções pré-definidas para cenários comuns.

1. Para iniciar uma implementação, **selecione Criar um recurso** a partir do portal [Azure](https://portal.azure.com).

   ![Novo recurso](./media/deploy-portal/new-resources.png)

1. Encontre o tipo de recurso que pretende utilizar. Os recursos são organizados em categorias. Se não vir a solução específica que pretende implementar, pode pesquisar no Marketplace por ela. A imagem que se segue mostra que o Ubuntu Server está selecionado.

   ![Selecione o tipo de recurso](./media/deploy-portal/select-resource-type.png)

1. Dependendo do tipo de recurso selecionado, tem uma coleção de propriedades relevantes para definir antes da implementação. Para todos os tipos, deve selecionar um grupo de recursos de destino. A imagem que se segue mostra como criar uma máquina virtual Linux e implantá-la para o grupo de recursos que criou.

   ![Criar grupo de recursos](./media/deploy-portal/select-existing-group.png)

   Pode decidir criar um grupo de recursos ao implementar os seus recursos. Selecione **Criar novo** e dar um nome ao grupo de recursos.

1. A sua implantação começa. A implantação pode levar vários minutos. Alguns recursos demoram mais tempo do que outros recursos. Quando a implementação terminar, vê uma notificação. Selecione **Ir para o recurso** para abrir

   ![Ver notificação](./media/deploy-portal/view-notification.png)

1. Depois de utilizar os seus recursos, pode adicionar mais recursos ao grupo de recursos selecionando **Add.**

   ![Adicionar recurso](./media/deploy-portal/add-resource.png)

Apesar de não o ter visto, o portal usou um modelo ARM para implementar os recursos que selecionou. Pode encontrar o modelo a partir do histórico de implementação. Para obter mais informações, consulte [o modelo de exportação após a implementação.](export-template-portal.md#export-template-after-deployment)

## <a name="deploy-resources-from-custom-template"></a>Implementar recursos a partir do modelo personalizado

Se quiser executar uma implementação mas não utilizar nenhum dos modelos no Marketplace, pode criar um modelo personalizado que define a infraestrutura para a sua solução. Para aprender a criar modelos, consulte [compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).

> [!NOTE]
> A interface do portal não suporta referências a um [segredo de um Cofre de Chaves](key-vault-parameter.md). Em vez disso, utilize [o PowerShell](deploy-powershell.md) ou [o Azure CLI](deploy-cli.md) para implementar o seu modelo localmente ou a partir de um URI externo.

1. Para implementar um modelo personalizado através do portal, selecione **Criar um recurso,** procurar **o modelo**. e, em seguida, selecione **a implementação do modelo**.

   ![Implementação do modelo de pesquisa](./media/deploy-portal/search-template.png)

1. Selecione **Criar**.
1. Você vê várias opções para criar um modelo:

    - **Construa o seu próprio modelo no editor:** Crie o seu próprio modelo no editor de modelo do portal.
    - **Modelos comuns**: Selecione a partir de soluções comuns.
    - **Carregue um modelo de arranque rápido GitHub**: Selecione entre [modelos](https://azure.microsoft.com/resources/templates/)de arranque rápido .

   ![Ver opções](./media/deploy-portal/see-options.png)

    Este tutorial fornece a instrução para o carregamento de um modelo de arranque rápido.

1. Em **'Carregar um modelo de arranque rápido' GitHub,** digite ou selecione **101-storage-account-create**.

    Tem duas opções:

    - **Selecione o modelo**: desloque o modelo.
    - **Modelo de edição**: edite o modelo de arranque rápido antes de o implementar.

1. Selecione **o modelo de edição** para explorar o editor de modelo do portal. O modelo está carregado no editor. Note que existem dois parâmetros: `storageAccountType` e `location` .

   ![Criar o modelo](./media/deploy-portal/show-json.png)

1. Faça uma pequena alteração no modelo. Por exemplo, atualizar a `storageAccountName` variável para:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selecione **Guardar**. Agora vê a interface de implementação do modelo do portal. Note os dois parâmetros que definiu no modelo.
1. Insira ou selecione os valores da propriedade:

    - **Assinatura**: Selecione uma subscrição Azure.
    - **Grupo de recursos**: Selecione **Criar novo** e dar um nome.
    - **Localização**: Selecione uma localização Azure.
    - **Tipo de Conta de Armazenamento**: Utilize o valor predefinido.
    - **Localização**: Utilize o valor predefinido.
    - **Aceito os termos e condições acima apresentados** (selecione)

1. Selecione **Comprar**.

## <a name="next-steps"></a>Passos seguintes

- Para ver os registos de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../management/view-activity-logs.md)
- Para resolver os erros de implementação, consulte [as operações de implantação da visualização](deployment-history.md).
- Para exportar um modelo de um grupo de implementação ou recursos, consulte [os modelos exporte ARM](export-template-portal.md).
- Para lançar o seu serviço com segurança em várias regiões, consulte [o Gestor de Implementação do Azure](deployment-manager-overview.md).
