---
title: Tutorial - Respostas da API ridículas em Gestão da API - Portal Azure / Microsoft Docs
description: Neste tutorial, você usa a API Management para definir uma política em uma API para que ele retorne uma resposta ridicularizada se o backend não estiver disponível para enviar respostas reais.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 78743c5f045f2544cafe88414ed996d08bacd2a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631125"
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

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Resposta da API ridicularizada":::

## <a name="add-an-operation-to-the-test-api"></a>Adicionar uma operação à API de teste

Uma API expõe uma ou mais operações. Nesta secção, adicione uma operação à API em branco que criou. Chamar a operação depois de concluir os passos nesta secção produz um erro. Não terá erros depois de completar os passos mais tarde na secção [De zombaria de resposta Ativa.](#enable-response-mocking)

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
1. **Selecione + Adicionar resposta**e selecione **200 OK** da lista.
1. No cabeçalho **Representações** à direita, selecione **+ Adicionar representação**.
1. Introduza *a aplicação/json* na caixa de pesquisa e selecione o tipo de conteúdo **de aplicação/json.**
1. Na caixa de texto **Exemplo**, introduza `{ "sampleField" : "test" }`.
1. Selecione **Guardar**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Resposta da API ridicularizada" border="false":::

Embora não sejam necessárias para este exemplo, as definições adicionais para uma operação API podem ser configuradas em outros separadores, incluindo:


|Tecla de Tabulação      |Descrição  |
|---------|---------|
|**Query**     |  Adicione parâmetros de consulta. Além de fornecer um nome e descrição, pode fornecer valores que são atribuídos a um parâmetro de consulta. Um dos valores pode ser marcado como predefinição (opcional).        |
|**Pedir**     |  Defina tipos de conteúdo de pedido, exemplos e esquemas.       |

## <a name="enable-response-mocking"></a>Ativar a simulação de respostas

1. Selecione a API criada na [Criar uma API de teste.](#create-a-test-api)
1. Selecione a operação de teste que adicionou.
1. Na janela à direita, certifique-se de que o separador **Design** está selecionado.
1. Na janela **de processamento de entrada,** selecione **+ Adicionar a política**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Resposta da API ridicularizada" border="false":::

1. **Selecione Respostas falsas** da galeria.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Resposta da API ridicularizada" border="false":::

1. Na caixa de texto **Respostas da Gestão de API**, escreva **200 OK, application/json**. Esta seleção indica que a API deve devolver o exemplo de resposta que definiu na secção anterior.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Resposta da API ridicularizada":::

1. Selecione **Guardar**.

    > [!TIP]
    > Uma barra amarela com o texto **Mocking está ativada** para a sua API indica que as respostas devolvidas da API Management enviam uma política de zombaria e não uma resposta real de backend.

## <a name="test-the-mocked-api"></a>Testar a API simulada

1. Selecione a API criada na [Criar uma API de teste.](#create-a-test-api)
1. Selecione o separador **Teste**.
1. Certifique-se de que a API **Chamada de teste** está selecionada. Selecione **Enviar** para efetuar uma chamada de teste.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Resposta da API ridicularizada":::

1. A **resposta de HTTP** apresenta o JSON fornecido como um exemplo na primeira secção do tutorial.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Resposta da API ridicularizada":::

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
