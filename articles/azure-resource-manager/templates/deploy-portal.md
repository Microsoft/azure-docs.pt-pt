---
title: Implementar recursos com portal Azure
description: Utilize o portal Azure e o Azure Resource Manage para implementar os seus recursos para um grupo de recursos na sua subscrição.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 32c807b4881bc59b6bec0d26ab3664abdb200628
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274362"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementar recursos com modelos do Resource Manager e do Portal do Azure

Aprenda a utilizar o [portal Azure](https://portal.azure.com) com o Gestor de [Recursos Azure](overview.md) para implementar os seus recursos Azure. Para aprender sobre a gestão dos seus recursos, consulte [gerir os recursos do Azure utilizando o portal Azure.](../management/manage-resources-portal.md)

A implantação de recursos Azure utilizando o portal Azure envolve geralmente dois passos:

- Crie um grupo de recursos.
- Desloque recursos para o grupo de recursos.

Além disso, também pode implementar um modelo de Gestor de Recursos Azure para criar recursos Azure.

Este artigo mostra ambos os métodos.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Para criar um novo grupo de recursos, selecione **Grupos de Recursos** do [portal Azure](https://portal.azure.com).

   ![Selecione grupos de recursos](./media/deploy-portal/select-resource-groups.png)

1. Sob os grupos de Recursos, selecione **Adicionar**.

   ![Adicionar grupo de recursos](./media/deploy-portal/add-resource-group.png)

1. Selecione ou introduza os seguintes valores de propriedade:

    - **Subscrição**: selecione uma subscrição do Azure.
    - **Grupo de recursos**: Dê ao grupo de recursos um nome.
    - **Região**: Especifique uma localização Azure. É aqui que o grupo de recursos armazena metadados sobre os recursos. Por razões de conformidade, pode querer especificar onde os metadados estão armazenados. Em geral, recomendamos que especifique um local onde a maioria dos seus recursos resida. Usar a mesma localização pode simplificar o seu modelo.

   ![Definir valores de grupo](./media/deploy-portal/set-group-properties.png)

1. Selecione **Rever + criar**.
1. rever os valores e, em seguida, selecionar **Criar**.
1. Selecione **Refresh** antes de poder ver o novo grupo de recursos na lista.

## <a name="deploy-resources-to-a-resource-group"></a>Implementar recursos para um grupo de recursos

Depois de criar um grupo de recursos, pode implementar recursos para o grupo a partir do Marketplace. O Marketplace fornece soluções pré-definidas para cenários comuns.

1. Para iniciar uma implementação, selecione **Criar um recurso** a partir do portal [Azure](https://portal.azure.com).

   ![Novo recurso](./media/deploy-portal/new-resources.png)

1. Encontre o tipo de recurso que gostaria de utilizar. Os recursos são organizados em categorias. Se não vir a solução em particular que gostaria de implementar, pode procurar no Marketplace por ela. A imagem que se segue mostra que o Ubuntu Server é selecionado.

   ![Selecione o tipo de recurso](./media/deploy-portal/select-resource-type.png)

1. Dependendo do tipo de recurso selecionado, tem uma coleção de propriedades relevantes para definir antes da implantação. Para todos os tipos, deve selecionar um grupo de recursos de destino. A imagem que se segue mostra como criar uma máquina virtual Linux e implantá-la para o grupo de recursos que criou.

   ![Criar grupo de recursos](./media/deploy-portal/select-existing-group.png)

   Em alternativa, pode decidir criar um grupo de recursos ao implementar os seus recursos. Selecione **Criar novo** e dar um nome ao grupo de recursos.

1. A tua missão começa. O destacamento pode levar alguns minutos. Alguns recursos demoram mais tempo do que outros recursos. Quando a implementação estiver concluída, verá uma notificação. Selecione **Ir ao recurso** para abrir

   ![Ver notificação](./media/deploy-portal/view-notification.png)

1. Depois de implementar os seus recursos, pode adicionar mais recursos ao grupo de recursos selecionando **Add**.

   ![Adicionar recurso](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementar recursos a partir de modelo personalizado

Se quiser executar uma implementação mas não utilizar nenhum dos modelos no Mercado, pode criar um modelo personalizado que define a infraestrutura para a sua solução. Para aprender sobre a criação de modelos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure.](template-syntax.md)

> [!NOTE]
> A interface do portal não suporta fazer referência a um [segredo de um Cofre chave.](key-vault-parameter.md) Em vez disso, utilize [o PowerShell](deploy-powershell.md) ou [o Azure CLI](deploy-cli.md) para implementar o seu modelo localmente ou a partir de um URI externo.

1. Para implementar um modelo personalizado através do portal, selecione **Criar um recurso,** procurar **modelo**. e, em seguida, selecione **a implementação do modelo**.

   ![Implantação do modelo de pesquisa](./media/deploy-portal/search-template.png)

1. Selecione **Criar**.
1. Você vê várias opções para criar um modelo:

    - **Construa o seu próprio modelo em editor:** crie um modelo usando o editor do modelo do portal.  O editor é capaz de adicionar um esquema de modelo de recursos.
    - **Modelos comuns**: Existem quatro modelos comuns para a criação de uma máquina virtual Linux, máquina virtual Windows, uma aplicação web e uma base de dados Azure SQL.
    - **Carregue um modelo de arranque rápido GitHub**: use um [modelo de arranque rápido](https://azure.microsoft.com/resources/templates/)existente .

   ![Ver opções](./media/deploy-portal/see-options.png)

    Este tutorial fornece a instrução para carregar um modelo de arranque rápido.

1. Sob **a carregue um modelo de arranque rápido GitHub,** escreva ou selecione **101-storage-account-create**.

    Tem duas opções:

    - **Selecione o modelo:** desdobrar o modelo.
    - **Modelo de edição**: edite o modelo de arranque rápido antes de o implementar.

1. Selecione **modelo de Edição** para explorar o editor de modelo do portal. O modelo está carregado no editor. Note que existem dois parâmetros: **armazenamentoAccountType** e **localização**.

   ![Criar o modelo](./media/deploy-portal/show-json.png)

1. Faça uma pequena alteração no modelo. Por exemplo, atualizar a **variável StorageAccountName** para:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selecione **Guardar**. Agora vê a interface de implantação do modelo do portal. Repare nos dois parâmetros que definiu no modelo.
1. Insira ou selecione os valores de propriedade:

    - **Subscrição**: selecione uma subscrição do Azure.
    - **Grupo de recursos**: Selecione **Criar novo** e dar um nome.
    - **Localização**: Selecione uma localização Azure.
    - **Tipo de conta**de armazenamento : Utilize o valor predefinido.
    - **Localização**: Utilize o valor predefinido.
    - **Aceito os termos e condições acima apresentados** (selecione)

1. Selecione **Comprar**.

## <a name="next-steps"></a>Passos Seguintes

- Para ver registos de auditoria, consulte [operações de auditoria com o Gestor de Recursos](../management/view-activity-logs.md).
- Para resolver erros de implantação, consulte as operações de [implantação do View](deployment-history.md).
- Para exportar um modelo de um grupo de implantação ou de recursos, consulte os modelos do [Export Azure Resource Manager](export-template-portal.md).
- Para lançar com segurança o seu serviço em várias regiões, consulte [o Gestor de Implantação do Azure.](deployment-manager-overview.md)
