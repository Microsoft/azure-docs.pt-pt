---
title: 'Tutorial: tutorial de entidade composta-LUIS'
titleSuffix: Azure Cognitive Services
description: Adicione uma entidade composta para agrupar dados extraídos de vários tipos em uma única entidade contentora. Ao agrupar os dados, o aplicativo cliente pode extrair facilmente os dados relacionados em diferentes tipos de dados.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: adb8941fd60a955a44a04717958c5203b721639a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498979"
---
# <a name="tutorial-group-and-extract-related-data"></a>Tutorial: agrupar e extrair dados relacionados
Neste tutorial, adicione uma entidade composta para agrupar dados extraídos de vários tipos em uma única entidade contentora. Ao agrupar os dados, o aplicativo cliente pode extrair facilmente os dados relacionados em diferentes tipos de dados.

A finalidade da entidade composta é agrupar entidades relacionadas em uma entidade de categoria pai. As informações existem como entidades separadas antes de uma composição ser criada. 

A entidade composta é uma boa opção para esse tipo de dados porque os dados:

* Estão relacionados entre si. 
* Use uma variedade de tipos de entidade.
* Têm de ser agrupadas e processadas pela aplicação cliente como uma unidade de informações.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar aplicativo de exemplo
> * Criar uma intenção
> * Adicionar entidade composta 
> * Preparar
> * Publicar
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar aplicativo de exemplo

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) no tutorial listar entidade.

2. Importe o JSON para uma nova aplicação.

3. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `composite`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

## <a name="composite-entity"></a>Entidade composta

Nesse aplicativo, o nome do departamento é definido na entidade lista de **departamentos** e inclui sinônimos. 

A intenção **TransferEmployeeToDepartment** tem um exemplo declarações para solicitar que um funcionário seja movido para um novo departamento. 

Exemplos de declarações para essa intenção incluem:

|Expressões de exemplo|
|--|
|mover John W. Smith para o departamento de contabilidade|
|transferir Jill Jones de para R & D|
 
A solicitação de movimentação deve incluir o nome do departamento e o nome do funcionário. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Adicione a entidade PersonName pré-criados para ajudar com a extração de tipo de dados comum

O LUIS oferece várias entidades pré-concebidas para extração de dados comuns. 

1. Selecione **criar** na parte superior da navegação e, em seguida, selecione **entidades** no menu de navegação à esquerda.

1. Selecione o botão **Gerir entidade pré-concebida**.

1. Selecione **[PersonName](luis-reference-prebuilt-person.md)** na lista de entidades predefinidas e selecione **concluído**.

    ![Captura de ecrã do número selecionado na caixa de diálogo de entidades pré-concebidas](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Essa entidade ajuda você a adicionar o reconhecimento de nome ao seu aplicativo cliente.

## <a name="create-composite-entity-from-example-utterances"></a>Criar uma entidade composta do exemplo declarações

1. Selecione **Intenções** no painel de navegação esquerdo.

1. Selecione **TransferEmployeeToDepartment** na lista de tentativas.

1. No `place John Jackson in engineering`expressão, selecione a entidade PersonName, `John Jackson`, em seguida, selecione **encapsule na entidade composta** na lista de menus pop-up para o seguinte expressão. 

    ![Captura de tela da seleção de encapsular composição na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Em seguida, selecione imediatamente a última entidade, `engineering` no expressão. Uma barra verde é desenhada sob as palavras selecionadas indicando uma entidade composta. No menu pop-up, insira o nome composto `TransferEmployeeInfo`, em seguida, selecione Enter. 

    ![Captura de tela da inserção do nome composto na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. Em **que tipo de entidade você deseja criar?** , todos os campos necessários estão na lista: `personName` e `Department`. Selecione **Done** (Concluído). Observe que a entidade predefinida, PersonName, foi adicionada à entidade composta. Se você puder ter uma entidade predefinida exibida entre os tokens inicial e final de uma entidade composta, a entidade composta deverá conter essas entidades predefinidas. Se as entidades predefinidas não forem incluídas, a entidade composta não será prevista corretamente, mas cada elemento individual será.

    ![Captura de tela da inserção do nome composto na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Exemplo de rótulo declarações com a entidade composta

1. Em cada exemplo de expressão, selecione a entidade mais à esquerda que deve estar na composição. Em seguida, selecione **encapsular na entidade composta**.

1. Selecione a última palavra na entidade composta e, em seguida, selecione **TransferEmployeeInfo** no menu pop-up. 

1. Verifique se todos os declarações na intenção estão rotulados com a entidade composta. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações na intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo treinado seja passível de consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão de intenção e entidade do ponto de extremidade 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá para o final do URL no endereço e introduza `Move Jill Jones to DevOps`. O último parâmetro QueryString é `q`, a consulta expressão. 

    Como esse teste é para verificar se o composto foi extraído corretamente, um teste pode incluir um expressão de exemplo existente ou um novo expressão. Um bom teste é incluir todas as entidades filho na entidade composta.

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

   Esse expressão retorna uma matriz de entidades compostas. Cada entidade recebe um tipo e um valor. Para obter mais precisão para cada entidade filho, use a combinação de tipo e valor do item da matriz composta para localizar o item correspondente na matriz de entidades.  

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial de entidade de lista](luis-quickstart-intents-only.md)
* Informações conceituais da [entidade composta](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos seguintes

Este tutorial criou uma entidade composta para encapsular entidades existentes. Isso permite que o aplicativo cliente encontre um grupo de dados relacionados em tipos diferentes para continuar a conversa. Um aplicativo cliente para este aplicativo de recursos humanos pode perguntar o dia e a hora em que a movimentação precisa começar e terminar. Ele também pode perguntar sobre outra logística da mudança, como um telefone físico. 

> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade simples com uma lista de frases](luis-quickstart-primary-and-secondary-data.md)  
