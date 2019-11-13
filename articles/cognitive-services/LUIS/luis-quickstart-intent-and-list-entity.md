---
title: 'Tutorial: correspondência de texto exata-LUIS'
titleSuffix: Azure Cognitive Services
description: Obtenha dados que correspondam a uma lista de itens predefinida. Cada item na lista pode ter sinónimos também com correspondência exata
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: a8021885bf51ab6d44bc8576b9fdd69f1bdd270a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953656"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Tutorial: obter dados de correspondência de texto exatos de um expressão

Neste tutorial, entenda como obter dados de entidade que correspondem a uma lista predefinida de itens. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar aplicativo
> * Adicionar intenções
> * Adicionar entidade de lista 
> * Preparar 
> * Publicar
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>O que é uma entidade de lista?

Uma entidade de lista é uma correspondência exata de texto com as palavras no expressão. 

Cada item na lista pode incluir uma lista de sinónimos. Para o aplicativo de recursos humanos, um departamento de empresa pode ser identificado por várias informações importantes, como um nome oficial, acrônimos comuns e códigos de departamento de cobrança. 

O aplicativo de recursos humanos precisa determinar o departamento ao qual um funcionário está transferindo. 

Uma entidade de lista é uma boa opção para este tipo de dados quando:

* Os valores dos dados são um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para este tipo de entidade.
* O texto na expressão é uma correspondência exata com um sinónimo ou o nome canónico. LUIS não usa a lista além de correspondências de texto exatas. A lematização, plurals e outras variações não são resolvidas com apenas uma entidade de lista. Para gerenciar variações, considere usar um [padrão](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional. 

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Criar uma intenção para transferir funcionários para um departamento diferente

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Create new intent** (Criar nova intenção). 

3. Introduza `TransferEmployeeToDepartment` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**. 

    ![Captura de ecrã da caixa de diálogo Criar nova de intenção com](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |mover John W. Smith para o departamento de contabilidade|
    |transferir Jill Jones de para R & D|
    |O departamento 1234 tem um novo membro chamado Bill Bradstreet|
    |Coloque John Jackson na engenharia |
    |mover Débora Doughtery para dentro de vendas|
    |MV Jill Jones a ele|
    |Shift Maria Anderson para DevOps|
    |Carl Chamerlin a Finance|
    |Steve Standish a 1234|
    |Tanner Thompson 3456|

    [![Captura de tela de intenção com exemplo declarações](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Captura de tela de intenção com exemplo declarações")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Entidade de lista de departamentos

Agora que a intenção **TransferEmployeeToDepartment** tem um exemplo de declarações, o Luis precisa entender o que é um departamento. 

O nome principal, _canônico_, para cada item é o nome do departamento. Exemplos dos sinônimos de cada nome canônico são: 

|Nome canônico|Sinónimos|
|--|--|
|Contabilidade|contas<br>como descontar<br>3456|
|Operações de desenvolvimento|Devops<br>4949|
|Engenharia|dação<br>motor<br>4567|
|Finanças|Fin<br>2020|
|Tecnologia da informação|TI<br>2323|
|Vendas internas|isale<br>invenda<br>1414|
|Pesquisa e desenvolvimento|R & D<br>1234|

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **Criar nova entidade**.

1. Na caixa de diálogo pop-up de entidade, introduza `Department` para o nome da entidade e **Lista** para o tipo de entidade. Selecione **Done** (Concluído).  

    [![Captura de tela de criando caixa de diálogo pop-up de nova entidade](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Captura de tela de criando caixa de diálogo pop-up de nova entidade")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Na página entidade do departamento, insira `Accounting` como o novo valor.

1. Para sinônimos, adicione os sinônimos da tabela anterior.

1. Continue adicionando todos os nomes canônicos e seus sinônimos. 

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar declarações de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações na intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo treinado seja passível de consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão de intenção e entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Vá para o final do URL no endereço e introduza `shift Joe Smith to IT`. O último parâmetro querystring é `q`, a expressão **query**. Esta expressão não é igual a qualquer uma das expressões identificadas, pelo que é um bom teste e deve devolver a intenção `TransferEmployeeToDepartment` com o `Department` extraído.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Listar](luis-concept-entity-types.md#list-entity) informações conceituais da entidade
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou uma nova intenção, adicionou expressões de exemplo e criou uma entidade de lista para extrair correspondências de texto exatas a partir de expressões. Depois de preparar e publicar a aplicação, uma consulta ao ponto final identifica a intenção e devolve os dados extraídos.

Continue com este aplicativo, [adicionando uma entidade composta](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Adicionar uma entidade predefinida com uma função ao aplicativo](tutorial-entity-roles.md)

