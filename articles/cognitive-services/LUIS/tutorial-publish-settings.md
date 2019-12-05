---
title: 'Tutorial: configurações de publicação-LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, altere as configurações de publicação para melhorar as previsões.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: 0e105d70ff7f590a84e0a82c15bcdd83052b63a8
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807493"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Tutorial: Adicionar análise de sentimentos como uma configuração de publicação

Neste tutorial, modifique as configurações de publicação para extrair a análise de sentimentos e, em seguida, consulte o ponto de extremidade LUIS para ver a opinião retornada de um usuário expressão.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Adicionar análise de sentimentos como uma configuração de publicação
> * Obtenha sentimentos de um expressão do ponto de extremidade publicado

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>A análise de sentimentos é uma configuração de publicação

As expressões seguintes mostram exemplos de sentimentos:

|Sentimento|Classificação|Expressão|
|:--|:--|:--|
|negativo|0.01 |A pizza foi horrível.|
|positiva|0,97 |A pizzaria do queijo foi maravilhosa.|

A análise de sentimentos é uma definição de publicação que se aplica a cada expressão. Uma vez definido, seu aplicativo retorna a suexpressãoção de um renomeação sem que você precise rotular dados.

Como é uma configuração de publicação, você não vê isso rotulado nas páginas intenções ou entidades. Pode vê-lo no painel [teste interativo](luis-interactive-test.md#view-sentiment-results) ou quando testar o URL de ponto final.

## <a name="import-example-json-to-begin-app"></a>Importar example. JSON para iniciar o aplicativo

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar a aplicação para incluir análise de sentimentos

1. Selecione **publicar** no menu superior. A análise de sentimentos é uma configuração de publicação.

1. Selecione **slot de produção** e, em seguida, selecione **alterar configurações**.
1. Defina a configuração de Análise de Sentimento como **ativado**.

    ![Ativar análise de sentimentos como a configuração de publicação](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Obter a opinião de um expressão do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL no endereço e insira o seguinte expressão:

    `Deliver 2 of the best cheese pizzas ever!!!`

    O último parâmetro querystring é `query`, a expressão **query**. Esta expressão não é igual a qualquer uma das expressões etiquetadas, pelo que é um bom teste e deve devolver a intenção `OrderPizza` com a análise de sentimentos extraída.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    A análise de sentimentos é positiva com uma pontuação de 86%.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* A análise de sentimentos é fornecida pelo [análise de texto](../Text-Analytics/index.yml)de serviço cognitiva. O recurso é restrito a Análise de Texto [idiomas com suporte](luis-language-support.md##languages-supported).
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, adicione a análise de sentimentos como uma definição de publicação para extrair valores de sentimentos da expressão como um todo.

> [!div class="nextstepaction"]
> [Rever expressões de ponto final na aplicação de RH](luis-tutorial-review-endpoint-utterances.md)

