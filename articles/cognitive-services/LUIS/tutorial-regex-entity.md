---
title: 'Tutorial: Entidade de expressão regular - LUIS'
titleSuffix: Azure Cognitive Services
description: Extrair dados consistentemente formatados de uma expressão utilizando a entidade de expressão regular.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 0ca6f2a67e01e4c604c2dcc8f8eaa9ffe8bad045
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75381533"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Tutorial: Obtenha dados bem formatados da expressão
Neste tutorial, crie uma entidade de expressão regular para extrair dados consistentemente formatados a partir de uma expressão.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App de importação
> * Adicionar intenções
> * Adicionar entidade de expressão regular
> * Treinar, publicar e consultar app para obter dados extraídos

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entidades de expressão regular

Utilize a entidade de expressão regular para retirar texto bem formatado de uma expressão. Embora a intenção da expressão seja sempre determinada por machine learning, este tipo de entidade específica não é de machine-learned. Um bom uso para a entidade de expressão regular é qualquer texto que possa ser consistentemente representado por uma [expressão regular](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

Este exemplo utiliza um _código curto_ para o envio de mensagens de texto. Este código curto é um código numérico de 5 ou 6 dígitos, `x\d{5,6}`pré-fixado com um x, e pode ser descrito com a expressão regular .

Quando adiciona uma entidade de expressão regular a uma aplicação LUIS, não precisa de [rotular](label-entity-example-utterance.md) o texto com a entidade expressa regular. É aplicado a todas as expressões em todas as intenções.

## <a name="import-example-json-to-begin-app"></a>Exemplo de importação .json para iniciar app

1.  Descarregue e guarde o [ficheiro JSON](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)da aplicação .

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Criar intenção de enviar mensagens de texto de confirmação

1. Selecione **+ Criar** para criar uma nova intenção de classificar a intenção de uma expressão para enviar um texto de confirmação.

1. Introduza `ConfirmationText` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**.

1. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |Envie o tempo de entrega da pizza para x123432|
    |Txt x234567 para o tempo|
    |x23987 para o aviso|

    Para extrair entidades aprendidas por máquinas, deve fornecer exemplos que incluem a entidade numa variedade de expressões mas com esta entidade não-aprendida por máquinas, a variação não é importante. Desde que o texto corresponda à expressão regular, será extraído.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Utilize a entidade de expressão regular para dados bem formatados
Crie uma entidade de expressão regular para corresponder ao número de texto. Esta expressão regular combina com texto, mas ignora variantes de caso e cultura.

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **+ Criar** na página da lista de Entidades.

1. No diálogo pop-up, introduza o `ConfirmationTextRegEx`nome da nova entidade, selecione **RegEx** como tipo de entidade e, em seguida, selecione **Next**.

    > [!div class="mx-imgBorder"]
    > ![Iniciar passos de criação de entidades para entidade de expressão regular](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. Na **Create a regex entity,** insira `x\d{5,6}` como o valor **Regex** e, em seguida, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Introduza expressão regular para extrair dados da expressão do exemplo](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Selecione **Intenções** a partir do menu esquerdo e, em seguida, o **Texto de Confirmação** pretende ver a expressão regular rotulada nas palavras.

    > [!div class="mx-imgBorder"]
    > ![Ver expressão regular rotulada em declarações de exemplo](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Como a entidade não é uma entidade aprendida por máquinas, a entidade é aplicada às declarações e exibida no portal LUIS assim que é criada.

## <a name="train-the-app-before-testing-or-publishing"></a>Treine a app antes de testar ou publicar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publique a app para consultar a partir do ponto final

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenha intenção e previsão de entidade a partir do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Vá até ao fim do URL no endereço e introduza a seguinte expressão:

    `Text my pizza delivery to x23456 x234567 x12345`

    O último parâmetro de `query`corda é, a **consulta**de expressão.

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Ao utilizar uma entidade de expressão regular, o LUIS extrai dados com nome, o que é mais útil programaticamente para a aplicação cliente receber a resposta JSON.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Conceito - entidades](luis-concept-entity-types.md)
* [Referência jSON de entidade de expressão regular](reference-entity-regular-expression.md?tabs=V3)
* [Como adicionar entidades para extrair dados](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Passos seguintes
Este tutorial criou uma nova intenção, adicionou expressões de exemplo e criou uma entidade de expressão regular para extrair dados bem formatados das expressões. Depois de preparar e publicar a aplicação, uma consulta ao ponto final identifica a intenção e devolve os dados extraídos.

> [!div class="nextstepaction"]
> [Saiba mais sobre a entidade de lista](tutorial-list-entity.md)

