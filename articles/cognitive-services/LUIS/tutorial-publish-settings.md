---
title: 'Tutorial: Publicar configurações - LUIS'
description: Neste tutorial, altere as definições de publicação para obter melhores previsões.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545778"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Tutorial: Adicionar análise de sentimento como um cenário de publicação

Neste tutorial, modifique as definições de publicação para extrair análise de sentimento e, em seguida, consultar o ponto final do LUIS para ver o sentimento devolvido de uma expressão do utilizador.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Adicione a análise de sentimento como uma definição de publicação
> * Obtenha o sentimento de uma expressão do ponto final publicado

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>A análise do sentimento é um cenário de publicação

As expressões seguintes mostram exemplos de sentimentos:

|Sentimento|Classificação|Expressão|
|:--|:--|:--|
|negativo|0.01 |A pizza estava horrível.|
|positiva|0.97 |A pizza de queijo era maravilhosa.|

A análise de sentimentos é uma definição de publicação que se aplica a cada expressão. Uma vez definida, a sua aplicação devolve o sentimento de uma expressão sem ter de rotular dados.

Por se trata de uma definição de publicação, não se vê rotulado nas páginas de intenções ou entidades. Pode vê-lo no painel [teste interativo](luis-interactive-test.md#view-sentiment-results) ou quando testar o URL de ponto final.

## <a name="import-example-json-to-begin-app"></a>Exemplo de importação .json para iniciar app

1.  Descarregue e guarde o [ficheiro JSON](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)da aplicação .

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar a aplicação para incluir análise de sentimentos

1. **Selecione Publicar** a partir do menu superior. A análise do sentimento é um cenário editorial.

1. Selecione **a ranhura de produção** e selecione as **definições de alteração**.
1. Defina a definição de Análise de Sentimento para **On**.

    ![Ligue a Análise de Sentimentos como definição de publicação](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Obtenha o sentimento de uma expressão do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até ao fim do URL na barra de endereços e substitua _YOUR_QUERY_HERE_ por:

    `Deliver 2 of the best cheese pizzas ever!!!`

    Esta expressão não é igual a qualquer uma das expressões etiquetadas, pelo que é um bom teste e deve devolver a intenção `OrderPizza` com a análise de sentimentos extraída.

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

    A análise de sentimento é positiva com uma pontuação de 86%.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* A análise de sentimento é fornecida pelo Cognitive Service [Text Analytics](../Text-Analytics/index.yml). A funcionalidade é restrita a [idiomas suportados](luis-language-support.md#languages-supported)por Text Analytics.
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, adicione a análise de sentimentos como uma definição de publicação para extrair valores de sentimentos da expressão como um todo.

> [!div class="nextstepaction"]
> [Rever expressões de ponto final na aplicação de RH](luis-tutorial-review-endpoint-utterances.md)

