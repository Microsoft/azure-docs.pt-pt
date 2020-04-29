---
title: 'Tutorial: Tutorial de entidade sita em compósito - LUIS'
description: Neste tutorial, adicione uma entidade composta para agregar dados extraídos de vários tipos numa única entidade contendo. Ao agregar os dados, a aplicação do cliente pode facilmente extrair dados relacionados em diferentes tipos de dados.
ms.topic: tutorial
ms.date: 03/31/2020
ms.openlocfilehash: 5b8185a56c54ec92ce8ceaf1cd029dd31f6e709c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478680"
---
# <a name="tutorial-group-and-extract-related-data"></a>Tutorial: Grupo e extrair dados relacionados
Neste tutorial, adicione uma entidade composta para agregar dados extraídos de vários tipos numa única entidade contendo. Ao agregar os dados, a aplicação do cliente pode facilmente extrair dados relacionados em diferentes tipos de dados.

O objetivo da entidade compósita é agrupar entidades relacionadas com uma entidade de categoria-mãe. A informação existe como entidades separadas antes da criação de um composto.

A entidade compósita é um bom ajuste para este tipo de dados porque os dados:

* Estão relacionados um com o outro.
* Use uma variedade de tipos de entidades.
* Têm de ser agrupadas e processadas pela aplicação cliente como uma unidade de informações.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App de exemplo de importação
> * Criar uma intenção
> * Adicionar entidade composta
> * Preparar
> * Publicar
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>App de exemplo de importação

1.  Descarregue e guarde o [ficheiro JSON](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) da entidade lista tutorial.

2. Importe o JSON numa nova app utilizando o [portal LUIS.](https://www.luis.ai)

3. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `composite`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

## <a name="composite-entity"></a>Entidade composta

Nesta aplicação, o nome do departamento é definido na entidade da lista do **Departamento** e inclui sinónimos.

A intenção **transferEmployeeToDepartment** tem exemplos de declarações para pedir que um empregado seja transferido para um novo departamento.

Exemplo de declarações para esta intenção incluem:

|Expressões de exemplo|
|--|
|mover John W. Smith para o departamento de contabilidade|
|transferir Jill Jones de R&D|

O pedido de mudança deve incluir o nome do departamento e o nome do empregado.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Adicione a entidade pré-construída PersonName para ajudar na extração comum do tipo de dados

O LUIS oferece várias entidades pré-concebidas para extração de dados comuns.

1. Selecione **Construir** a partir da navegação superior e, em seguida, selecione **Entidades** do menu de navegação esquerdo.

1. Selecione o botão **Gerir entidade pré-concebida**.

1. Selecione **[PersonName](luis-reference-prebuilt-person.md)** da lista de entidades pré-construídas e, em seguida, selecione **Done**.

    ![Captura de ecrã do número selecionado na caixa de diálogo de entidades pré-concebidas](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Esta entidade ajuda-o a adicionar reconhecimento de nome à sua aplicação de cliente.

## <a name="create-composite-entity-from-example-utterances"></a>Criar entidade compósita a partir de declarações de exemplo

1. Selecione **Intenções** no painel de navegação esquerdo.

1. Selecione **TransferEmployeeToDepartment** da lista de intenções.

1. Na expressão `place John Jackson in engineering`, selecione a `John Jackson`entidade personName, e selecione **Wrap em entidade composta** na lista de menus pop-up para a seguinte expressão.

    ![Screenshot de selecionar compósito de embrulho em diálogo drop down](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Em seguida, selecione `engineering` imediatamente a última entidade, na expressão. Uma barra verde é desenhada sob as palavras selecionadas indicando uma entidade composta. No menu pop-up, introduza `TransferEmployeeInfo` o nome composto e, em seguida, selecione introduzir.

    ![Screenshot de introduzir o nome composto no diálogo drop down](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. Em **que tipo de entidade pretende criar?** `personName` `Department` Selecione **Done** (Concluído). Note que a entidade pré-construída, personName, foi adicionada à entidade composta. Se puder ter uma entidade pré-construída entre o início e o fim de fichas de uma entidade composta, a entidade compósita deve conter essas entidades pré-construídas. Se as entidades pré-construídas não estiverem incluídas, a entidade composta não está corretamente prevista, mas cada elemento individual está.

    ![Screenshot de introduzir o nome composto no diálogo drop down](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Pronunciamentos de exemplo de etiqueta com entidade composta

1. Em cada exemplo, selecione a entidade mais à esquerda que deve estar no composto. Em seguida, **selecione Wrap em entidade composta**.

1. Selecione a última palavra na entidade composta e, em seguida, selecione **TransferEmployeeInfo** do menu pop-up.

1. Verifique se todas as declarações na intenção estão etiquetadas com a entidade composta.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treine a app para que as alterações à intenção possam ser testadas

Para treinar a aplicação, selecione **Train**. A formação aplica as alterações, como as novas entidades e as expressões rotuladas, ao modelo ativo.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publique a app para aceder a ela a partir do ponto final http

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenha intenção e previsão de entidade a partir do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá para o final do URL no endereço e introduza `Move Jill Jones to DevOps`. O último parâmetro querystring é `q`, a expressão query.

    Uma vez que este teste é para verificar se o composto é extraído corretamente, um teste pode incluir uma expressão de amostra existente ou uma nova expressão. Um bom teste é incluir todas as entidades infantis na entidade composta.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Esta expressão devolve uma matriz de entidades compostas. A cada entidade é dado um tipo e valor. Para encontrar mais precisão para cada entidade infantil, utilize a combinação de tipo e valor do item de matriz composto para encontrar o item correspondente na matriz de entidades.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial de entidade de lista](luis-quickstart-intents-only.md)
* [Informação](luis-concept-entity-types.md) conceptual de entidade seleção composta
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos seguintes

Este tutorial criou uma entidade composta para encapsular as entidades existentes. Isto permite que a aplicação do cliente encontre um grupo de dados relacionados em diferentes tipos de dados para continuar a conversação. Uma aplicação de clientes para esta aplicação de Recursos Humanos poderia perguntar a que dia e hora o movimento precisa de começar e terminar. Também poderia perguntar sobre outras logísticas do movimento, como um telefone físico.

> [!div class="nextstepaction"]
> [Corrigir previsões inseguras através da revisão das declarações de pontofinal](luis-tutorial-review-endpoint-utterances.md)
