---
title: 'Tutorial: Rever as declarações de ponto final - LUIS'
description: Neste tutorial, melhore as previsões da aplicação verificando ou corrigindo as expressões recebidas através do ponto final LUIS HTTP de que a LUIS não tem a certeza. Algumas expressões podem ser validadas para a intenção e outras podem ter de ser validadas para a entidade.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/02/2020
ms.openlocfilehash: b8f8fa2cd3c9c22187bb95c55d9de2abb2e8caec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324642"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Tutorial: Corrigir previsões inseguras através da revisão das declarações de ponto final
Neste tutorial, melhore as previsões das aplicações verificando ou corrigindo as expressões, recebidas através do ponto final LUIS HTTPS, de que a LUIS não tem a certeza. Você deve rever as declarações de ponto final como uma parte regular da sua manutenção agendada LUIS.

Este processo de revisão permite ao LUIS aprender o seu domínio de aplicação. LUIS seleciona as expressões que aparecem na lista de revisão. Esta lista é:

* Específica da aplicação.
* Destina-se a melhorar a precisão de predição da aplicação.
* Deve ser revista periodicamente.

Ao rever as expressões de ponto final, está a validar ou corrigir a intenção prevista da expressão.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App de exemplo de importação
> * Rever pronunciações de ponto final
> * Treinar e publicar app
> * Consultar o ponto final da aplicação para ver a resposta JSON de LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>Baixar ficheiro JSON para app

Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

## <a name="import-json-file-for-app"></a>Importar ficheiro JSON para aplicação


1. No [portal LUIS,](https://www.luis.ai)na página **My apps,** selecione **+ Nova aplicação para conversação,** em seguida **Import as JSON**. Encontre o ficheiro JSON guardado do passo anterior. Não precisa de mudar o nome da aplicação. Selecione **Feito**

1. Selecione **Build** então **Intenções** para ver as intenções, os principais blocos de construção de uma aplicação LUIS.

    :::image type="content" source="media/luis-tutorial-review-endpoint-utterances/initial-intents-in-app.png" alt-text="Altere da página Versões para a página Intenções.":::

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Treine a app para aplicar as alterações da entidade à app

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publique a app para aceder a partir do ponto final HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Adicione expressões no ponto final

Nesta aplicação, tem intenções e entidades, mas não tem qualquer utilização no ponto final. Esta utilização do ponto final é necessária para melhorar a aplicação com a revisão da expressão de ponto final.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até ao final do URL na barra de endereços e substitua _YOUR_QUERY_HERE_ pelas expressões na tabela seguinte. Para cada expressão, submeta a expressão e obtenha o resultado. Em seguida, substitua a expressão no final com a próxima expressão.

    |Expressão de ponto final|Intenção alinhada|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Existe um único conjunto de expressões para rever, independentemente da versão que está ativamente a editar ou da versão da aplicação que foi publicada no ponto final.

## <a name="review-endpoint-utterances"></a>Rever pronunciações de ponto final

Reveja as declarações do ponto final para uma intenção corretamente alinhada. Embora exista um único conjunto de expressões para rever em todas as versões, o processo de alinhamento correta da intenção adiciona apenas o exemplo do _modelo ativo_ atual.

1. A partir da secção **Build** do portal, selecione Rever as **expressões** do ponto final da navegação à esquerda. A lista está filtrada para a intenção **ApplyForJob**.

    :::image type="content" source="./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png" alt-text="Altere da página Versões para a página Intenções.":::

    Esta `I'm looking for a job with Natural Language Processing` expressão, não está na intenção correta, _GetJobInformation_. Foi mal indiciado como _ApplyForJob_ devido à semelhança de nomes de emprego e verbos nas duas intenções.

1.  Para alinhar esta expressão, selecione a **intenção** alinhada correta de `GetJobInformation` . Adicione a expressão alterada à aplicação selecionando a marca de verificação.

    Reveja as restantes declarações nesta intenção, corrigindo a intenção alinhada conforme necessário. Use a tabela de expressão inicial neste tutorial para ver a intenção alinhada.

    A lista **de declarações de ponto final de revisão** não deve continuar a ter as expressões corrigidas. Se aparecerem mais expressões, continue a trabalhar através da lista, corrigindo as intenções alinhadas até que a lista esteja vazia.

    Qualquer correção da rotulagem da entidade é feita após o alinhamento da intenção, a partir da página de detalhes da Intenção.

1. Prepare e publique novamente a aplicação.

## <a name="get-intent-prediction-from-endpoint"></a>Obtenha previsão de intenção a partir do ponto final

Para verificar as expressões de exemplo corretamente alinhadas melhorou a previsão da app, experimente uma expressão próxima da expressão corrigida.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até ao final do URL na barra de endereços e substitua _YOUR_QUERY_HERE_ por `Are there any natural language processing jobs in my department right now?` .

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.901367366
                },
                "ApplyForJob": {
                    "score": 0.0307973567
                },
                "EmployeeFeedback": {
                    "score": 0.0296942145
                },
                "MoveEmployee": {
                    "score": 0.00739785144
                },
                "FindForm": {
                    "score": 0.00449316856
                },
                "Utilities.Stop": {
                    "score": 0.00417657848
                },
                "Utilities.StartOver": {
                    "score": 0.00407167152
                },
                "Utilities.Help": {
                    "score": 0.003662492
                },
                "None": {
                    "score": 0.00335733569
                },
                "Utilities.Cancel": {
                    "score": 0.002225436
                },
                "Utilities.Confirm": {
                    "score": 0.00107437756
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2020-07-02 21:45:50"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
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

   Agora que as expressões inseguras estão corretamente alinhadas, a intenção correta foi prevista com uma **pontuação elevada.**

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>A revisão pode ser substituída ao adicionar mais expressões?
Pode questionar-se sobre o motivo pelo qual não deve adicionar mais expressões de exemplo. Qual é o objetivo da revisão de expressões de ponto final? Numa aplicação LUIS do mundo real, as expressões de ponto final são provenientes de utilizadores com uma escolha e disposição de palavras que ainda não utilizou. Se tivesse utilizado a mesma escolha e disposição de palavras, a predição original teria uma percentagem mais elevada.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Por que motivo a intenção principal está na lista de expressões?
Algumas expressões de ponto final terão uma classificação de predição elevada na lista de revisão. Ainda tem de rever e validar essas expressões. Estão na lista porque a intenção mais alta seguinte tinha uma classificação demasiado próxima da classificação da intenção principal. Pretende ter cerca de 15% de diferença entre as duas intenções principais.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, reviu as expressões submetidas ao ponto final, que o LUIS não conseguiu assegurar. Depois destas expressões terem sido validadas e movidas para as intenções corretas como expressões de exemplo, o LUIS irá melhorar a exatidão da predição.

> [!div class="nextstepaction"]
> [Saiba como utilizar padrões](luis-tutorial-pattern.md)
