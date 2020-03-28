---
title: 'Tutorial: Revisão das declarações de pontofinal - LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, melhore as previsões da aplicação verificando ou corrigindo as declarações recebidas através do ponto final luis http que luis não tem certeza. Algumas expressões podem ser validadas para a intenção e outras podem ter de ser validadas para a entidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 06f51ca83449b39861e7565cc9accc29efbece3f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843978"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Tutorial: Corrigir previsões inseguras através da revisão das declarações de pontofinal
Neste tutorial, melhore as previsões da aplicação verificando ou corrigindo as declarações, recebidas através do ponto final luis HTTPS, de que o LUIS não tem a certeza. Você deve rever as declarações de ponto final como uma parte regular da sua manutenção agendada do LUIS.

Este processo de revisão permite que a LUIS aprenda o domínio da sua aplicação. Luis seleciona as palavras que aparecem na lista de revisão. Esta lista é:

* Específica da aplicação.
* Destina-se a melhorar a precisão de predição da aplicação.
* Deve ser revista periodicamente.

Ao rever as expressões de ponto final, está a validar ou corrigir a intenção prevista da expressão.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App de exemplo de importação
> * Rever pronunciações de ponto final
> * Treinar e publicar app
> * Consultar o ponto final da aplicação para ver a resposta JSON de LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>App de exemplo de importação

Utilize os seguintes passos para importar uma aplicação.

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true).

1. Na [pré-visualização](https://preview.luis.ai)do portal LUIS, importe o ficheiro .json para uma nova app.

1. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `review`.

    > [!TIP]
    > Clonar numa nova versão é uma boa prática antes de modificar a sua aplicação. Quando terminar uma versão, exporte a versão (como um ficheiro .json ou .lu) e verifique o ficheiro no seu sistema de controlo de origem.


1. Para treinar a aplicação, selecione **Train**.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publique a app para aceder a ela a partir do ponto final http

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Adicione expressões no ponto final

Nesta aplicação, tem intenções e entidades, mas não tem qualquer utilização de ponto final. Este uso de ponto final é necessário para melhorar a aplicação com a revisão de expressão de ponto final.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Utilize o ponto final para adicionar as seguintes expressões.

    |Expressão de endpoint|Intenção alinhada|
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

Reveja as declarações finais para uma intenção corretamente alinhada. Embora exista um único conjunto de expressões para rever em todas as versões, o processo de alinhamento correto da intenção adiciona o exemplo apenas ao _modelo ativo_ atual.

1. A partir da secção **Build** do portal, selecione **Comentários finais** da navegação à esquerda. A lista está filtrada para a intenção **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Captura de ecrã do botão para Rever expressões de ponto final na navegação à esquerda](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Esta expressão, `I'm looking for a job with Natural Language Processing`não está na intenção correta.

1.  Para alinhar esta expressão, na linha de expressão, selecione a **intenção alinhada** correta de `GetJobInformation`. Adicione a expressão alterada à aplicação selecionando a marca de verificação.

    > [!div class="mx-imgBorder"]
    > ![Captura de ecrã do botão para Rever expressões de ponto final na navegação à esquerda](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Reveja as restantes declarações nesta intenção, corrigindo a intenção alinhada conforme necessário. Utilize a tabela inicial de expressão neste tutorial para ver a intenção alinhada.

    A lista de declarações de **pontofinal** de revisão já não deve ter as declarações corrigidas. Se aparecerem mais declarações, continue a trabalhar através da lista, corrigindo as intenções alinhadas até que a lista esteja vazia.

    Qualquer correção da rotulagem da entidade é feita após a intenção estar alinhada, a partir da página de detalhes da Intenção.

1. Prepare e publique novamente a aplicação.

## <a name="get-intent-prediction-from-endpoint"></a>Obtenha previsão de intenção a partir do ponto final

Para verificar as declarações corretamente alinhadas do exemplo, melhorou a previsão da aplicação, tente uma expressão próxima da expressão corrigida.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá para o final do URL no endereço e introduza `Are there any natural language processing jobs in my department right now?`. O último parâmetro de `q`corda é, a **consulta**de expressão.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
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
                                        "value": "2019-12-05 23:23:53"
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

   Agora que as declarações inseguras estão corretamente alinhadas, a intenção correta foi prevista com uma **pontuação elevada**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>A revisão pode ser substituída ao adicionar mais expressões?
Pode questionar-se sobre o motivo pelo qual não deve adicionar mais expressões de exemplo. Qual é o objetivo da revisão de expressões de ponto final? Numa aplicação LUIS do mundo real, as expressões de ponto final são provenientes de utilizadores com uma escolha e disposição de palavras que ainda não utilizou. Se tivesse utilizado a mesma escolha e disposição de palavras, a predição original teria uma percentagem mais elevada.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Por que motivo a intenção principal está na lista de expressões?
Algumas expressões de ponto final terão uma classificação de predição elevada na lista de revisão. Ainda tem de rever e validar essas expressões. Estão na lista porque a intenção mais alta seguinte tinha uma classificação demasiado próxima da classificação da intenção principal. Pretende ter cerca de 15% de diferença entre as duas intenções principais.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, reviu as expressões submetidas ao ponto final, que o LUIS não conseguiu assegurar. Depois destas expressões terem sido validadas e movidas para as intenções corretas como expressões de exemplo, o LUIS irá melhorar a exatidão da predição.

> [!div class="nextstepaction"]
> [Saiba como utilizar padrões](luis-tutorial-pattern.md)
