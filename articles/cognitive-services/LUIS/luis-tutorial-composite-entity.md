---
title: Tutorial de entidade composta-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione uma entidade composta para reunir dados extraídos de vários tipos com uma única entidade contentora. Ao agrupar os dados, a aplicação cliente pode, facilmente, extrair dados relacionados em diferentes tipos de dados.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 698635b3f216c556e1e36a033703b8786a028e38
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946137"
---
# <a name="tutorial-group-and-extract-related-data"></a>Tutorial: Agrupar e extrair dados relacionados
Neste tutorial, adicione uma entidade composta para reunir dados extraídos de vários tipos com uma única entidade contentora. Ao agrupar os dados, a aplicação cliente pode, facilmente, extrair dados relacionados em diferentes tipos de dados.

O objetivo da entidade composto consiste em Agrupar entidades relacionadas numa entidade da categoria principal. As informações existem como entidades separadas antes da criação de uma composição. 

A entidade composta é uma boa opção para este tipo de dados, porque os dados:

* Estão relacionados entre si. 
* Utilize uma variedade de tipos de entidade.
* Têm de ser agrupadas e processadas pela aplicação cliente como uma unidade de informações.

**Neste tutorial, vai aprender a:**

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

## <a name="composite-entity"></a>entidade composta

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

1. No expressão `place John Jackson in engineering`, selecione a entidade PersonName, `John Jackson`, em seguida, selecione encapsule **na entidade composta** na lista de menus pop-up para o seguinte expressão. 

    ![Captura de tela da seleção de encapsular composição na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Em seguida, selecione imediatamente a última entidade `engineering` na expressão. Uma barra de verde é desenhada sob as palavras selecionadas que indica uma entidade composta. No menu de pop-up, introduza o nome composto `TransferEmployeeInfo` , em seguida, selecione introduzir. 

    ![Captura de tela da inserção do nome composto na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. Em **que tipo de entidade você deseja criar?** , todos os campos necessários estão na lista: `personName` e. `Department` Selecione **Done** (Concluído). Observe que a entidade predefinida, PersonName, foi adicionada à entidade composta. Se poderia ter uma entidade pré-criados aparecem entre o início e a terminar em tokens de uma entidade composta, a entidade composta tem de conter essas entidades criados previamente. Se a entidades pré-concebidas não estão incluídas, a entidade composta não está prevista corretamente, mas é de cada elemento individual.

    ![Captura de tela da inserção do nome composto na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Expressões com de exemplo de etiqueta com a entidade composta

1. Em cada ocorrência de pronunciação de exemplo, selecione a entidade de mais à esquerda que deve estar na composição. Em seguida, selecione **encapsular na entidade composta**.

1. Selecione a última palavra na entidade composta e, em seguida, selecione **TransferEmployeeInfo** no menu pop-up. 

1. Certifique-se de que todas as expressões na intenção são rotuladas com a entidade composta. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações na intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo treinado seja passível de consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão de intenção e entidade do ponto de extremidade 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá para o final do URL no endereço e introduza `Move Jill Jones to DevOps`. O último parâmetro de cadeia de consulta é `q`, a consulta de expressão. 

    Uma vez que este teste é verificar que a composição é extraída corretamente, um teste ou pode incluir uma expressão de exemplo existente ou uma expressão de novo. É um bom teste incluir todas as entidades de subordinado na entidade composta.

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

   Esta expressão devolve uma matriz de entidades compostos. Cada entidade é dado um tipo e valor. Para localizar mais de precisão de cada entidade subordinada, utilize a combinação de tipo e o valor do item de matriz compostos para localizar o item correspondente da matriz de entidades.  

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial de entidade de lista](luis-quickstart-intents-only.md)
* Informações conceituais da [entidade composta](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criado uma entidade composta para encapsular entidades existentes. Isso permite que a aplicação de cliente localizar um grupo de dados relacionados em diferentes tipos de dados para continuar a conversa. Uma aplicação de cliente para esta aplicação de recursos humanos pode pedir que dia e hora a movimentação tem de começar e terminar. Ele também pode perguntar sobre outra logística da mudança, como um telefone físico. 

> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade com uma lista de frase](luis-quickstart-primary-and-secondary-data.md)  
