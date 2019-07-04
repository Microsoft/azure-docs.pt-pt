---
title: Utilizar o portal do Azure para implementar recursos do Azure | Documentos da Microsoft
description: Utilize o portal do Azure e Azure Resource Manager para implementar os recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460344"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementar recursos com modelos do Resource Manager e do Portal do Azure

Saiba como utilizar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para implementar os recursos do Azure. Para saber mais sobre a gestão dos seus recursos, veja [recursos de gerir o Azure com o portal do Azure](manage-resources-portal.md).

Implementar recursos do Azure ao utilizar o portal do Azure geralmente envolve duas etapas:

- Crie um grupo de recursos.
- Implemente recursos no grupo de recursos.

Além disso, também pode implementar um modelo do Azure Resource Manager para criar recursos do Azure.

Este artigo mostra os dois métodos.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Para criar um novo grupo de recursos, selecione **grupos de recursos** partir do [portal do Azure](https://portal.azure.com).

   ![Selecione os grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Em grupos de recursos, selecione **adicionar**.

   ![Adicionar grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Selecione ou introduza os seguintes valores de propriedade:

    - **Subscrição**: Selecione uma subscrição do Azure.
    - **Grupo de recursos**: Dê um nome ao grupo de recursos.
    - **Região**: Especifique uma localização do Azure. Isso é onde o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez queira especificar onde esses metadados estão armazenados. Em geral, recomendamos que especificar uma localização onde a maioria dos seus recursos irão residir. Use o mesmo local pode simplificar o seu modelo.

   ![Definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Selecione **Rever + criar**.
1. Reveja os valores e, em seguida, selecione **criar**.
1. Selecione **atualizar** antes de pode ver o novo grupo de recursos na lista.

## <a name="deploy-resources-to-a-resource-group"></a>Implementar recursos para um grupo de recursos

Depois de criar um grupo de recursos, pode implementar recursos para o grupo do Marketplace. O Marketplace proporciona soluções predefinidas para cenários comuns.

1. Para iniciar uma implementação, selecione **criar um recurso** partir do [portal do Azure](https://portal.azure.com).

   ![Novo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Encontre o tipo de recurso que pretende implementar. Os recursos estão organizados em categorias. Se não vir a solução específica que pretende implementar, pode procurar no Marketplace para o mesmo. Captura de ecrã seguinte mostra que o servidor Ubuntu está selecionado.

   ![Selecione o tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Dependendo do tipo de recursos selecionado, tem uma coleção de propriedades relevantes para definir antes da implantação. Para todos os tipos, tem de selecionar um grupo de recursos de destino. A imagem seguinte mostra como criar uma máquina virtual Linux e implementá-la para o grupo de recursos que criou.

   ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Em alternativa, pode optar por criar um grupo de recursos durante a implantação de seus recursos. Selecione **criar novo** e dê um nome ao grupo de recursos.

1. Início da sua implantação. A implementação pode demorar vários minutos. Alguns recursos demoram mais tempo do que outros recursos. Quando a implementação estiver concluída, verá uma notificação. Selecione **Ir para recurso** para abrir

   ![Notificação de vista](./media/resource-group-template-deploy-portal/view-notification.png)

1. Depois de implementar os seus recursos, pode adicionar mais recursos para o grupo de recursos, selecionando **adicionar**.

   ![Adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementar recursos de modelo personalizado

Se quiser executar uma implementação, mas não utilizar qualquer um dos modelos no Marketplace, pode criar um modelo personalizado que define a infraestrutura para a sua solução. Para saber mais sobre a criação de modelos, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> Interface do portal não suporta a referenciar um [segredo a partir de um cofre de chave](resource-manager-keyvault-parameter.md). Em alternativa, utilize [PowerShell](resource-group-template-deploy.md) ou [CLI do Azure](resource-group-template-deploy-cli.md) para implementar o modelo localmente ou a partir de um URI externo.

1. Para implementar um modelo personalizado através do portal, selecione **criar um recurso**, procure **modelo**. e, em seguida, selecione **implementação do modelo**.

   ![Implementação do modelo de pesquisa](./media/resource-group-template-deploy-portal/search-template.png)

1. Selecione **Criar**.
1. Verá várias opções para criar um modelo:

    - **Criar seu próprio modelo no editor**: criar um modelo através do modelo do portal de editor.  O editor é capaz de adicionar um esquema de modelo de recurso.
    - **Modelos comuns**: Existem quatro templatess comuns para a criação de uma máquina virtual do Linux, máquinas de virtuais do Windows, um aplicativo web e uma base de dados SQL do Azure.
    - **Carregar um modelo de início rápido do GitHub**: Utilize um existente [modelos de início rápido](https://azure.microsoft.com/resources/templates/).

   ![Opções de visualização](./media/resource-group-template-deploy-portal/see-options.png)

    Este tutorial fornece instruções para carregar um modelo de início rápido.

1. Sob **carregar um modelo de início rápido do GitHub**, escreva ou selecione **101-storage-account-create**.

    Tem duas opções:

    - **Selecionar modelo**: implementar o modelo.
    - **Editar modelo**: editar o modelo de início rápido, antes de o implementar.

1. Selecione **Editar modelo** para explorar o editor de modelo do portal. O modelo é carregado no editor. Observe que há dois parâmetros: **storageAccountType** e **localização**.

   ![Criar o modelo](./media/resource-group-template-deploy-portal/show-json.png)

1. Fazer uma alteração menor ao modelo. Por exemplo, atualizar o **storageAccountName** variável à:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selecione **Guardar**. Agora verá a interface de implementação do modelo do portal. Observe os dois parâmetros definidos no modelo.
1. Introduza ou selecione os valores de propriedade:

    - **Subscrição**: Selecione uma subscrição do Azure.
    - **Grupo de recursos**: Selecione **criar novo** e dê um nome.
    - **Localização**: Selecione uma localização do Azure.
    - **Tipo de conta de armazenamento**: Utilize o valor predefinido.
    - **Localização**: Utilize o valor predefinido.
    - **Aceito os termos e condições acima apresentados** (selecione)

1. Selecione **Comprar**.

## <a name="next-steps"></a>Passos Seguintes

- Para ver os registos de auditoria, consulte [auditar operações com o Resource Manager](./resource-group-audit.md).
- Para resolver erros de implementação, consulte o artigo [ver as operações de implementação](./resource-manager-deployment-operations.md).
- Para exportar um modelo a partir de uma implementação ou o grupo de recursos, consulte [modelos Azure Resource Manager exportar](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- De forma segura distribuir o seu serviço em várias regiões, consulte [Gestor de implementação do Azure](./deployment-manager-overview.md).
