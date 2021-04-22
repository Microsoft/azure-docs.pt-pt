---
title: Tutorial - Respostas de API ridículas em Gestão API - Portal Azure | Microsoft Docs
description: Neste tutorial, você usa a API Management para definir uma política em uma API para que ele retorne uma resposta ridicularizada se o backend não estiver disponível para enviar respostas reais.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 6214594f825908388762ac35cc8c8722ff9135f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874356"
---
# <a name="tutorial-mock-api-responses"></a>Tutorial: Respostas da API falsas

As APIs de backend podem ser importadas para uma API de Gestão de API (APIM) ou criadas e geridas manualmente. Os passos neste tutorial mostram-lhe como usar a APIM para criar uma API em branco e geri-la manualmente, em seguida, definir uma política em uma API para que ele retorne uma resposta ridicularizada. Este método permite aos programadores continuar a implementação e o teste da instância de APIM mesmo que o back-end não esteja disponível para enviar respostas reais. 

A capacidade de simular respostas pode ser útil em vários cenários:

+ Quando a fachada de API é concebida primeiro e a implementação de back-end é efetuada mais tarde. Quando o back-end está a ser desenvolvido em paralelo.
+ Quando o back-end está temporariamente não operacional ou não consegue dimensionar.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma API de teste 
> * Adicionar uma operação à API de teste
> * Ativar a simulação de respostas
> * Testar a API simulada


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Resposta da API ridicularizada":::

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Compreender o [conceito das políticas da Gestão de API do Azure](api-management-howto-policies.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Criar uma API de teste 

Os passos nesta secção mostram como criar uma API em branco sem back-end. 


1. Inscreva-se no portal Azure e navegue para a sua instância de Gestão da API.
1. Selecione **APIs**  >  **+ Adicione**  >  **API Em Branco API**.
1. Na janela **Create a API em branco,** selecione **Full**.
1. Introduza *a API de teste* para o nome do **visor**.
1. Selecione **Ilimitado** para **Produtos.**
1. Certifique-se de que **o Managed** é selecionado em **Gateways**.
1. Selecione **Criar**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Criar API em branco":::

## <a name="add-an-operation-to-the-test-api"></a>Adicionar uma operação à API de teste

Uma API expõe uma ou mais operações. Nesta secção, adicione uma operação à API em branco que criou. Chamar a operação depois de concluir os passos nesta secção produz um erro. Não terá erros depois de completar os passos mais tarde na secção [De zombaria de resposta Ativa.](#enable-response-mocking)

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione a API que criou no passo anterior.
1. Selecione **+ Adicionar Operação**.
1. Na janela **Frontend, introduza** os seguintes valores.

     | Definição             | Valor                             | Descrição                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome a apresentar**    | *Chamada de teste*                       | O nome que é apresentado no portal do [desenvolvedor](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **URL** (verbo HTTP) | GET                               | Selecione um dos verbos HTTP pré-definidos.                                                                                                                                         |
    | **URL**             | */teste*                           | Um caminho de URL para a API.                                                                                                                                                                       |
    | **Descrição**     |                                   |  Descrição opcional da operação, utilizada para fornecer documentação no portal do desenvolvedor aos desenvolvedores que utilizam esta API.                                                    |
    
1. Selecione o separador **Respostas,** localizado nos campos URL, Nome do Visor e Descrição. Introduza as definições neste separador para definir códigos de estado de resposta, tipos de conteúdo, exemplos e esquemas.
1. **Selecione + Adicionar resposta** e selecione **200 OK** da lista.
1. No cabeçalho **Representações** à direita, selecione **+ Adicionar representação**.
1. Introduza *a aplicação/json* na caixa de pesquisa e selecione o tipo de conteúdo **de aplicação/json.**
1. Na caixa de texto **Exemplo**, introduza `{ "sampleField" : "test" }`.
1. Selecione **Guardar**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Adicionar operação API" border="false":::

Embora não sejam necessárias para este exemplo, as definições adicionais para uma operação API podem ser configuradas em outros separadores, incluindo:


|Tecla de Tabulação      |Description  |
|---------|---------|
|**Query**     |  Adicione parâmetros de consulta. Além de fornecer um nome e descrição, pode fornecer valores que são atribuídos a um parâmetro de consulta. Um dos valores pode ser marcado como predefinição (opcional).        |
|**Pedir**     |  Defina tipos de conteúdo de pedido, exemplos e esquemas.       |

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar O Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para adicionar uma operação à API de teste, executar a [operação az apim api criar](/cli/azure/apim/api/operation#az_apim_api_operation_create) comando:

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Executar o comando [da lista de operações da az apim api](/cli/azure/apim/api/operation#az_apim_api_operation_list) para ver todas as suas operações para uma API:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Para remover uma operação, utilize a [operação az apim api eliminar](/cli/azure/apim/api/operation#az_apim_api_operation_delete) o comando. Obtenha a identificação da operação do comando anterior.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Guarde esta operação para utilização no resto deste artigo.

---

## <a name="enable-response-mocking"></a>Ativar a simulação de respostas

1. Selecione a API criada na [Criar uma API de teste.](#create-a-test-api)
1. Selecione a operação de teste que adicionou.
1. Na janela à direita, certifique-se de que o separador **Design** está selecionado.
1. Na janela **de processamento de entrada,** selecione **+ Adicionar a política**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Adicionar política de processamento" border="false":::

1. **Selecione Respostas falsas** da galeria.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Mosaico da política de simulação de respostas" border="false":::

1. Na caixa de texto **Respostas da Gestão de API**, escreva **200 OK, application/json**. Esta seleção indica que a API deve devolver o exemplo de resposta que definiu na secção anterior.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Definir resposta de zombaria":::

1. Selecione **Guardar**.

    > [!TIP]
    > Uma barra amarela com o texto **Zombaria para** a sua API indica que as respostas devolvidas da API Management são ridicularizadas pela [política de zombaria](api-management-advanced-policies.md#mock-response) e não são produzidas pelo backend.

## <a name="test-the-mocked-api"></a>Testar a API simulada

1. Selecione a API criada na [Criar uma API de teste.](#create-a-test-api)
1. Selecione o separador **Teste**.
1. Certifique-se de que a API **Chamada de teste** está selecionada. Selecione **Enviar** para efetuar uma chamada de teste.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Testar a API simulada":::

1. A **resposta de HTTP** apresenta o JSON fornecido como um exemplo na primeira secção do tutorial.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Resposta ridícula HTTP":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma API de teste
> * Adicionar uma operação à API de teste
> * Ativar a simulação de respostas
> * Testar a API simulada

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
