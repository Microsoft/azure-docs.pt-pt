---
title: Usar portal do Azure para implantar recursos do Azure | Microsoft Docs
description: Use portal do Azure e o gerenciamento de recursos do Azure para implantar seus recursos em um grupo de recursos em sua assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: 197bd14c425d80a814f370a2050c085b1eef143d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579023"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementar recursos com modelos do Resource Manager e do Portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [Azure Resource Manager](resource-group-overview.md) para implantar os recursos do Azure. Para saber mais sobre como gerenciar seus recursos, consulte [gerenciar recursos do Azure usando o portal do Azure](manage-resources-portal.md).

A implantação de recursos do Azure usando o portal do Azure geralmente envolve duas etapas:

- Crie um grupo de recursos.
- Implante recursos no grupo de recursos.

Além disso, você também pode implantar um modelo de Azure Resource Manager para criar recursos do Azure.

Este artigo mostra os dois métodos.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Para criar um novo grupo de recursos, selecione **grupos de recursos** no [portal do Azure](https://portal.azure.com).

   ![Selecionar grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Em grupos de recursos, selecione **Adicionar**.

   ![Adicionar grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Selecione ou insira os seguintes valores de propriedade:

    - **Subscrição**: selecione uma subscrição do Azure.
    - **Grupo de recursos**: dê um nome ao grupo de recursos.
    - **Região**: especifique um local do Azure. É aí que o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, recomendamos que você especifique um local onde a maioria dos recursos residirá. Usar o mesmo local pode simplificar seu modelo.

   ![Definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Selecione **Rever + criar**.
1. Examine os valores e, em seguida, selecione **criar**.
1. Selecione **Atualizar** antes de poder ver o novo grupo de recursos na lista.

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um grupo de recursos, você pode implantar recursos no grupo por meio do Marketplace. O Marketplace fornece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, selecione **criar um recurso** no [portal do Azure](https://portal.azure.com).

   ![Novo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Localize o tipo de recurso que você deseja implantar. Os recursos são organizados em categorias. Se você não vir a solução específica que deseja implantar, poderá pesquisar o Marketplace para ele. A captura de tela a seguir mostra que o servidor Ubuntu está selecionado.

   ![Selecionar tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades relevantes para definir antes da implantação. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar uma máquina virtual do Linux e implantá-la no grupo de recursos que você criou.

   ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Como alternativa, você pode optar por criar um grupo de recursos ao implantar seus recursos. Selecione **criar novo** e dê um nome ao grupo de recursos.

1. Sua implantação começa. A implantação pode levar vários minutos. Alguns recursos levam mais tempo do que outros recursos. Quando a implantação for concluída, você verá uma notificação. Selecione **ir para o recurso** para abrir

   ![Exibir notificação](./media/resource-group-template-deploy-portal/view-notification.png)

1. Depois de implantar seus recursos, você pode adicionar mais recursos ao grupo de recursos selecionando **Adicionar**.

   ![Adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implantar recursos do modelo personalizado

Se você quiser executar uma implantação, mas não usar nenhum dos modelos no Marketplace, poderá criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre a criação de modelos, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> A interface do portal não dá suporte à referência a um [segredo de um Key Vault](resource-manager-keyvault-parameter.md). Em vez disso, use o [PowerShell](resource-group-template-deploy.md) ou [CLI do Azure](resource-group-template-deploy-cli.md) para implantar seu modelo localmente ou de um URI externo.

1. Para implantar um modelo personalizado por meio do portal, selecione **criar um recurso**, Pesquisar **modelo**. e, em seguida, selecione **implantação de modelo**.

   ![Implantação de modelo de pesquisa](./media/resource-group-template-deploy-portal/search-template.png)

1. Selecione **Criar**.
1. Você verá várias opções para criar um modelo:

    - Crie **seu próprio modelo no editor**: Crie um modelo usando o editor de modelos de Portal.  O editor é capaz de adicionar um esquema de modelo de recurso.
    - **Modelos comuns**: há quatro modelos comuns para criar uma máquina virtual Linux, uma máquina virtual do Windows, um aplicativo Web e um banco de dados SQL do Azure.
    - **Carregar um modelo de início rápido do GitHub**: use [modelos de início rápido](https://azure.microsoft.com/resources/templates/)existentes.

   ![Opções de exibição](./media/resource-group-template-deploy-portal/see-options.png)

    Este tutorial fornece a instrução para carregar um modelo de início rápido.

1. Em **carregar um modelo de início rápido do GitHub**, digite ou selecione **101-armazenamento-conta-criar**.

    Tem duas opções:

    - **Selecionar modelo**: implantar o modelo.
    - **Editar modelo**: Edite o modelo de início rápido antes de implantá-lo.

1. Selecione **Editar modelo** para explorar o editor de modelo de Portal. O modelo é carregado no editor. Observe que há dois parâmetros: **storageAccountType** e **Location**.

   ![Criar o modelo](./media/resource-group-template-deploy-portal/show-json.png)

1. Faça uma alteração secundária no modelo. Por exemplo, atualize a variável **storageAccountName** para:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selecione **Guardar**. Agora você vê a interface de implantação de modelo do Portal. Observe os dois parâmetros que você definiu no modelo.
1. Insira ou selecione os valores de propriedade:

    - **Subscrição**: selecione uma subscrição do Azure.
    - **Grupo de recursos**: selecione **criar novo** e dê um nome.
    - **Local**: selecione um local do Azure.
    - **Tipo de conta de armazenamento**: Use o valor padrão.
    - **Local**: Use o valor padrão.
    - **Aceito os termos e condições acima apresentados** (selecione)

1. Selecione **Comprar**.

## <a name="next-steps"></a>Passos seguintes

- Para exibir os logs de auditoria, consulte [operações de auditoria com o Gerenciador de recursos](./resource-group-audit.md).
- Para solucionar problemas de erros de implantação, consulte [Exibir operações de implantação](./resource-manager-deployment-operations.md).
- Para exportar um modelo de uma implantação ou grupo de recursos, consulte [exportar modelos de Azure Resource Manager](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Para distribuir com segurança seu serviço em várias regiões, consulte [Deployment Manager do Azure](./deployment-manager-overview.md).
