---
title: Simular respostas de API com o portal do Azure | Microsoft Docs
description: Este tutorial mostra como utilizar a Gestão de API (APIM) para definir uma política numa API de modo a devolver uma resposta simulada. Este método permite aos programadores continuar a implementação e o teste da instância de Gestão de API no caso de o back-end não estar disponível para enviar respostas reais.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 9b9a691cb2bce2357d184420912ab340aee534e8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205312"
---
# <a name="mock-api-responses"></a>Simular respostas de API

As APIs de back-end podem ser importadas para uma API de APIM ou criadas e geridas manualmente. Os passos neste tutorial mostram como utilizar o APIM para criar uma API em branco e geri-la manualmente. O tutorial mostra como definir uma política numa API de modo a devolver uma resposta simulada. Este método permite aos programadores continuar a implementação e o teste da instância de APIM mesmo que o back-end não esteja disponível para enviar respostas reais. A capacidade de simular respostas pode ser útil em vários cenários:

+ Quando a fachada de API é concebida primeiro e a implementação de back-end é efetuada mais tarde. Quando o back-end está a ser desenvolvido em paralelo.
+ Quando o back-end está temporariamente não operacional ou não consegue dimensionar.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma API de teste 
> * Adicionar uma operação à API de teste
> * Ativar a simulação de respostas
> * Testar a API simulada

![Resposta de operação simulada](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Compreender o [conceito das políticas da Gestão de API do Azure](api-management-howto-policies.md).
+ Conclua o início rápido seguinte: [Criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Criar uma API de teste 

Os passos nesta secção mostram como criar uma API em branco sem back-end. Mostra também como adicionar uma operação à API. Chamar a operação depois de concluir os passos nesta secção produz um erro. Não obterá erros depois de concluir os passos na secção "Ativar a simulação de respostas".

![Criar API em branco](./media/mock-api-responses/03-MockAPIResponses-01-CreateTestAPI.png)

1. Selecione **APIs** no serviço **Gestão de API**.
2. No menu à esquerda, selecione **+ Adicionar API**.
3. Selecione **API em branco** na lista.
4. Introduza "*API de teste*" para **Nome a apresentar**.
5. Introduza "*Ilimitados*" para **Produtos**.
6. Selecione **Criar**.

## <a name="add-an-operation-to-the-test-api"></a>Adicionar uma operação à API de teste

![Adicionar operação à API](./media/mock-api-responses/03-MockAPIResponses-02-AddOperation.png)

1. Selecione a API que criou no passo anterior.
2. Clique em **+ Adicionar Operação**.

    | Definição             | Valor                             | Descrição                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome a apresentar**    | *Chamada de teste*                       | O nome que é apresentado no **Portal do programador**.                                                                                                                                       |
    | **URL** (verbo HTTP) | GET                               | Pode escolher um dos verbos HTTP predefinidos.                                                                                                                                         |
    | **URL**             | */test*                           | Um caminho de URL para a API.                                                                                                                                                                       |
    | **Descrição**     |                                   | Forneça uma descrição da operação utilizada para fornecer a documentação aos programadores que utilizam esta API no **Portal do programador**.                                                    |
    | Separador **Consulta**       |                                   | Pode adicionar parâmetros de consulta. Além de fornecer um nome e uma descrição, pode fornecer valores que podem ser atribuídos a este parâmetro. Um dos valores pode ser marcado como predefinição (opcional). |
    | Separador **Pedido**     |                                   | Pode definir os tipos de conteúdo, exemplos e esquemas do pedido.                                                                                                                                  |
    | Separador **Resposta**    | Veja os passos que se seguem nesta tabela. | Defina códigos de estado de resposta, tipos de conteúdo, exemplos e esquemas.                                                                                                                           |

3. Selecione o separador **Resposta**, localizado abaixo dos campos URL, Nome a apresentar e Descrição.
4. Clique em **+ Adicionar resposta**.
5. Selecione **200 OK** na lista.
6. No cabeçalho **Representações** à direita, selecione **+ Adicionar representação**.
7. Introduza "*application/json*" na caixa de pesquisa e selecione o tipo de conteúdo **application/json**.
8. Na caixa de texto **Exemplo**, introduza `{ 'sampleField' : 'test' }`.
9. Selecione **Criar**.

## <a name="enable-response-mocking"></a>Ativar a simulação de respostas

![Ativar a simulação de respostas](./media/mock-api-responses/03-MockAPIResponses-03-EnableMocking.png)

1. Selecione a API que criou no passo "Criar uma API de teste".
2. Selecione a operação de teste que adicionou.
3. Na janela à direita, clique no separador **Conceber**.
4. Na janela **Processamento de entrada**, clique em **+Adicionar política**.
5. Selecione o mosaico **Simular respostas** da galeria.

    ![Mosaico da política de simulação de respostas](./media/mock-api-responses/mock-responses-policy-tile.png)

6. Na caixa de texto **Respostas da Gestão de API**, escreva **200 OK, application/json**. Esta seleção indica que a API deve devolver o exemplo de resposta que definiu na secção anterior.

    ![Ativar a simulação de respostas](./media/mock-api-responses/mock-api-responses-set-mocking.png)

7. Clique em **Guardar**.

## <a name="test-the-mocked-api"></a>Testar a API simulada

![Testar a API simulada](./media/mock-api-responses/03-MockAPIResponses-04-TestMocking.png)

1. Selecione a API que criou no passo "Criar uma API de teste".
2. Abra o separador **Testar**.
3. Certifique-se de que a API **Chamada de teste** está selecionada.

    > [!TIP]
    > Uma barra amarela com o texto **A simulação está ativada** indica que as respostas devolvidas da Gestão de API enviam uma política de simulação e não uma resposta de back-end real.

4. Selecione **Enviar** para efetuar uma chamada de teste.
5. A **resposta de HTTP** apresenta o JSON fornecido como um exemplo na primeira secção do tutorial.

    ![Ativar a simulação de respostas](./media/mock-api-responses/mock-api-responses-test-response.png)

## <a name="video"></a>Vídeo

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma API de teste
> * Adicionar uma operação à API de teste
> * Ativar a simulação de respostas
> * Testar a API simulada

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
