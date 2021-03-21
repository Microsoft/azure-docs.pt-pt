---
title: Tutorial - Publicar versões da sua API utilizando a Azure API Management
description: Siga os passos deste tutorial para aprender a publicar várias versões API na API Management.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: apimpm
ms.openlocfilehash: f6ea02c32ec7fcb694d63f29c63c3880a7cfff9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546657"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Tutorial: Publicar várias versões da sua API 

Há alturas em que é impraticável ter todos os chamadores para a sua API a usar exatamente a mesma versão. Quando os chamadores querem fazer upgrade para uma versão posterior, querem uma abordagem que seja fácil de entender. Como mostrado neste tutorial, é possível fornecer *várias versões* na Azure API Management. 

Para obter em segundo plano, consulte [versões & revisões](https://azure.microsoft.com/blog/versions-revisions/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma nova versão a uma API existente
> * Escolher um esquema de versão
> * Adicionar a versão a um produto
> * Navegar até ao portal do programador para ver a versão

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Versão mostrada no portal Azure":::

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

## <a name="add-a-new-version"></a>Adicionar uma nova versão

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Selecione **APIs**.
1. Selecione **Demo Conference API** na lista de APIs. 
1. Selecione o menu de contexto **(...**) ao lado **da API da Conferência de Demonstração**.
1. Selecione **a versão Adicionar**.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="Menu de contexto API - adicionar versão":::


> [!TIP]
> As versões também podem ser ativadas quando se cria uma nova API. No ecrã **Add API,** selecione **Versão desta API?**

## <a name="choose-a-versioning-scheme"></a>Escolher um esquema de versões

Na Azure API Management, você escolhe como os chamadores especificam a versão API selecionando um *esquema de versão*: **caminho, cabeçalho** ou **cadeia de consulta**. No exemplo seguinte, o *caminho* é usado como o esquema de versão.

Introduza os valores a partir da tabela seguinte. Em seguida, **selecione Criar** para criar a sua versão.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Adicionar janela de versão":::



|Definição   |Valor  |Descrição  |
|---------|---------|---------|
|**Nome**     |  *demo-conference-api-v1*       |  Nome único no seu caso de Gestão de API.<br/><br/>Como uma versão é, de facto, uma nova API baseada na [revisão](api-management-get-started-revise-api.md)da API , esta definição é o nome da nova API.   |
|**Esquema de versão**     |  **Caminho**       |  A forma como os chamadores especificam a versão API.     |
|**Identifer versão**     |  *v1*       |  Indicador específico do esquema da versão. For **Path**, o sufixo para o caminho URL da API. <br/><br/> Se **o cabeçalho** ou a corda de consulta forem **selecionados,** insira um valor adicional: o nome do cabeçalho ou parâmetro de cadeia de consulta.<br/><br/> É apresentado um exemplo de utilização.        |
|**Produtos**     |  **Ilimitado**       |  Opcionalmente, um ou mais produtos a que a versão API está associada. Para publicar a API, precisa de associá-la a um produto. Também pode [adicionar a versão a um produto](#add-the-version-to-a-product) mais tarde.      |

Depois de criar a versão, aparece agora por baixo **da API da Conferência de Demo** na Lista de API. Agora vê duas APIs: **Original,** e **v1**.

![Versões listadas numa API no portal do Azure](media/api-management-getstarted-publish-versions/version-list.png)

Agora pode editar e configurar **v1** como uma API que está separada do **Original**. As alterações a uma versão não afetam a outra.

> [!Note]
> Se adicionar uma versão a uma API não ver versão, um **Original** também é criado automaticamente. Esta versão responde no URL predefinido. A criação de uma versão Original garante que os chamadores existentes não são quebrados pelo processo de adicionar uma versão. Se criar uma nova API com versões ativadas no início, não é criado um Original.

## <a name="add-the-version-to-a-product"></a>Adicionar a versão a um produto

Para os autores de chamadas verem a nova versão, esta tem de ser adicionada a um *produto*. Se ainda não adicionou a versão a um produto, pode adicioná-la a um produto a qualquer momento.

Por exemplo, para adicionar a versão ao produto **Ilimitado:**
1. No portal Azure, navegue para o seu caso de Gestão API.
1. Selecione **Produtos**  >  **APIs**  >    >  **ilimitados + adicionar.**
1. Selecione **Demo Conference API**, versão **v1**.
1. Clique em **Selecionar**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Adicionar versão ao produto":::

## <a name="use-version-sets"></a>Use conjuntos de versão

Ao criar várias versões, o portal Azure cria um conjunto de *versões*, que representa um conjunto de versões para uma única API lógica. Selecione o nome de uma API que tem várias versões. O portal Azure apresenta o seu **conjunto de versão**. Pode personalizar o **Nome** e **Descrição** de um conjunto virtual.

Pode interagir diretamente com os conjuntos de versão utilizando o Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para ver todos os conjuntos de versão, executar o comando [da lista de versões az apim api:](/cli/azure/apim/api/versionset#az_apim_api_versionset_list)

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Quando o portal Azure cria uma versão definida para si, atribui um nome alfanumérico, que aparece na coluna **Nome** da lista. Utilize este nome em outros comandos Azure CLI.

Para ver detalhes sobre um conjunto de versão, executar o comando de exibição de [versões az apim api:](/api/versionset#az_apim_api_versionset_show)

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

Para obter mais informações sobre os conjuntos de versões, consulte [versões na Azure API Management](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>Navegar até ao portal do programador para ver a versão

Se experimentou o portal do [desenvolvedor,](api-management-howto-developer-portal-customize.md)pode ver as versões da API lá.

1. Selecione **Portal do Programador** no menu superior.
2. Selecione **APIs** e, em seguida, selecione **Demo Conference API**.
3. Você deve ver um dropdown com várias versões ao lado do nome API.
4. Selecione **v1**.
5. Repare no **URL do pedido** da primeira operação na lista. Indica que o caminho do URL da API inclui **v1**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma nova versão a uma API existente
> * Escolher um esquema de versão 
> * Adicionar a versão a um produto
> * Navegar até ao portal do programador para ver a versão

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Personalizar o estilo das páginas de portal do Programador](api-management-howto-developer-portal-customize.md)
