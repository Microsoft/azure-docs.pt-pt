---
title: 'Tutorial: regular-entidade de expressão-LUIS'
titleSuffix: Azure Cognitive Services
description: Extraia dados formatados consistentemente de um expressão usando a entidade expressão regular.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381533"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Tutorial: obter dados bem formatados do expressão
Neste tutorial, crie uma entidade de expressão regular para extrair dados formatados consistentemente de um expressão.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar aplicativo
> * Adicionar intenções
> * Adicionar entidade de expressão regular
> * Treinar, publicar e consultar o aplicativo para obter dados extraídos

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entidades de expressão regular

Use a entidade expressão regular para extrair texto bem formatado de um expressão. Embora a intenção da expressão seja sempre determinada por machine learning, este tipo de entidade específica não é de machine-learned. Um bom uso para a entidade de expressão regular é qualquer texto que possa ser representado consistentemente por uma [expressão regular](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

Este exemplo usa um _código curto_ para enviar mensagens de texto. Esse código curto é um código numérico de 5 ou 6 dígitos, prefixado com um x e pode ser descrito com a expressão regular `x\d{5,6}`.

Quando você adiciona uma entidade de expressão regular a um aplicativo LUIS, não precisa [rotular](label-entity-example-utterance.md) o texto com a entidade expressa normal. Ele é aplicado a todos os declarações em todas as intenções.

## <a name="import-example-json-to-begin-app"></a>Importar example. JSON para iniciar o aplicativo

1.  Baixe e salve o [arquivo JSON do aplicativo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Criar intenção para enviar mensagens de texto de confirmação

1. Selecione **+ criar** para criar uma nova intenção de classificar uma intenção de expressão para enviar um texto de confirmação.

1. Introduza `ConfirmationText` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**.

1. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |Enviar o tempo de entrega de pizza para x123432|
    |X234567 txt para hora|
    |x23987 para o aviso|

    Para extrair entidades aprendidas por computador, você deve fornecer exemplos que incluam a entidade em uma variedade de declarações, mas com essa entidade não aprendida por máquina, a variação não é importante. Desde que o texto corresponda à expressão regular, ele será extraído.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Usar a entidade de expressão regular para dados bem formatados
Crie uma entidade de expressão regular para corresponder ao número de texto. Essa expressão regular corresponde ao texto, mas ignora as variantes de maiúsculas e minúsculas.

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **+ criar** na página lista de entidades.

1. Na caixa de diálogo pop-up, insira o nome da nova entidade `ConfirmationTextRegEx`, selecione **Regex** como o tipo de entidade e, em seguida, selecione **Avançar**.

    > [!div class="mx-imgBorder"]
    > ![iniciar as etapas de criação de entidade para a entidade de expressão regular](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. Na **entidade criar uma Regex**, insira `x\d{5,6}` como o valor **Regex** e, em seguida, selecione **criar**.

    > [!div class="mx-imgBorder"]
    > ![inserir uma expressão regular para extrair dados do exemplo expressão](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Selecione **tentativas** no menu à esquerda, em seguida, a intenção **ConfirmationText** para ver a expressão regular rotulada em declarações.

    > [!div class="mx-imgBorder"]
    > ![exibir a expressão regular rotulada no exemplo declarações](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Como a entidade não é uma entidade aprendida por máquina, a entidade é aplicada ao declarações e exibida no portal do LUIS assim que é criada.

## <a name="train-the-app-before-testing-or-publishing"></a>Treinar o aplicativo antes de testar ou publicar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar o aplicativo para consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão de intenção e entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Vá para o final da URL no endereço e insira o seguinte expressão:

    `Text my pizza delivery to x23456 x234567 x12345`

    O último parâmetro querystring é `query`, a expressão **query**.

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

* [Conceito – entidades](luis-concept-entity-types.md)
* [Referência JSON de entidade de expressão regular](reference-entity-regular-expression.md?tabs=V3)
* [Como adicionar entidades para extrair dados](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Passos seguintes
Este tutorial criou uma nova intenção, adicionou expressões de exemplo e criou uma entidade de expressão regular para extrair dados bem formatados das expressões. Depois de preparar e publicar a aplicação, uma consulta ao ponto final identifica a intenção e devolve os dados extraídos.

> [!div class="nextstepaction"]
> [Saiba mais sobre a entidade de lista](tutorial-list-entity.md)

