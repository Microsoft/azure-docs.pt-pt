---
title: Correspondência exata de texto
titleSuffix: Azure Cognitive Services
description: Obtenha dados que correspondam a uma lista de itens predefinida. Cada item na lista pode ter sinónimos também com correspondência exata
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 23a94f7b2054692feedb3b7755dad1e840809be7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734123"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Tutorial: Obter exatamente os dados correspondentes de texto de uma expressão

Neste tutorial, saiba como obter dados de entidade que corresponde a uma lista predefinida de itens. 

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar aplicação
> * Adicionar intenções
> * Adicionar entidade de lista 
> * Preparar 
> * Publicar
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>O que é uma entidade de lista?

Uma entidade de lista é uma correspondência exata de texto para as palavras a expressão. 

Cada item na lista pode incluir uma lista de sinónimos. Para a aplicação de recursos humanos, um departamento da empresa pode ser identificado por várias informações cruciais, como um nome oficial, acrônimos comuns e faturação códigos de departamento. 

Tem de determinar o departamento de que um funcionário é a transferência para a aplicação de recursos humanos. 

Uma entidade de lista é uma boa opção para este tipo de dados quando:

* Os valores dos dados são um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para este tipo de entidade.
* O texto na expressão é uma correspondência exata com um sinónimo ou o nome canónico. LUIS não usa a lista para além de correspondências de texto exato. Lematização plurais e outras variações não estão resolvidas com apenas uma entidade de lista. Para gerir variações, considere a utilização de um [padrão](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional. 

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Criar uma intenção de transferir os funcionários para outro departamento

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Create new intent** (Criar nova intenção). 

3. Introduza `TransferEmployeeToDepartment` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**. 

    ![Captura de ecrã da caixa de diálogo Criar nova de intenção com](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |mover o João Silva de W. para o departamento de contabilidade|
    |Transferir Jill Jones partir para p & D|
    |Departamento 1234 tem um novo membro com o nome Bradstreet de fatura|
    |Coloque o John Jackson em engenharia |
    |Mover Debra Doughtery para dentro de vendas|
    |mV Jill Jones para IT|
    |SHIFT Alice Anderson para DevOps|
    |Carl Chamerlin para finanças|
    |Steve Standish para 1234|
    |Thompson Tanner para 3456|

    [![Captura de ecrã da intenção com expressões de exemplo](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "captura de ecrã da intenção com expressões de exemplo")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Entidade de lista do departamento

Agora que o **TransferEmployeeToDepartment** intenção tem expressões de exemplo, LUIS precisa entender o que é um departamento. 

O principal _canônico_, nome para cada item é o nome do departamento. São exemplos de sinónimos de cada nome canônico: 

|Nome canónico|Sinónimos|
|--|--|
|Contabilidade|acct<br>accting<br>3456|
|Operações de desenvolvimento|Devops<br>4949|
|Engenharia|engenharia<br>motor<br>4567|
|Finanças|FIN<br>2020|
|Tecnologia da informação|TI<br>2323|
|Vendas por dentro|isale<br>insale<br>1414|
|Pesquisa e desenvolvimento|P &AMP; D<br>1234|

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **Criar nova entidade**.

1. Na caixa de diálogo pop-up de entidade, introduza `Department` para o nome da entidade e **Lista** para o tipo de entidade. Selecione **Done** (Concluído).  

    [![Captura de ecrã da criação de caixa de diálogo de pop-up entidade novo](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "captura de ecrã da criação de caixa de diálogo de pop-up entidade novo")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Na página de entidade de departamento, introduza `Accounting` como o novo valor.

    [![Captura de ecrã da introdução de valor](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "captura de ecrã da introdução de valor")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. Para sinónimos, adicione os sinónimos da tabela anterior.

    [![Captura de ecrã da introdução de sinónimos](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "captura de ecrã da introdução sinónimos")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. Continue a adicionar todos os nomes canonical e seus sinónimos. 

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar expressões de exemplo para a intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Preparar a aplicação para que as alterações à intenção podem ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar a aplicação, para que o modelo preparado é consultável do ponto final

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão intenções e entidades do ponto final

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

* [Listar entidades](luis-concept-entity-types.md#list-entity) informações concetuais
* [Como dar formação](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, criou uma nova intenção, adicionou expressões de exemplo e criou uma entidade de lista para extrair correspondências de texto exatas a partir de expressões. Depois de preparar e publicar a aplicação, uma consulta ao ponto final identifica a intenção e devolve os dados extraídos.

Continuar com esta aplicação, [adicionar uma entidade composta](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Adicione uma entidade hierárquica à aplicação](luis-quickstart-intent-and-hier-entity.md)

