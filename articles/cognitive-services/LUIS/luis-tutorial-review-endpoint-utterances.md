---
title: 'Tutorial: revisando o ponto de extremidade declarações-LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, melhore as previsões de aplicativo verificando ou corrigindo declarações recebidas por meio do ponto de extremidade HTTP LUIS que LUIS não tem certeza. Algumas expressões podem ser validadas para a intenção e outras podem ter de ser validadas para a entidade.
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843978"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Tutorial: corrigir previsões inseguras examinando o ponto de extremidade declarações
Neste tutorial, melhore as previsões de aplicativo verificando ou corrigindo declarações, recebidos por meio do ponto de extremidade HTTPS LUIS, que LUIS não tem certeza de. Você deve examinar o ponto de extremidade declarações como uma parte normal da manutenção agendada do LUIS.

Esse processo de revisão permite que o LUIS Aprenda seu domínio de aplicativo. LUIS seleciona o declarações que aparece na lista de revisão. Esta lista é:

* Específica da aplicação.
* Destina-se a melhorar a precisão de predição da aplicação.
* Deve ser revista periodicamente.

Ao rever as expressões de ponto final, está a validar ou corrigir a intenção prevista da expressão.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar aplicativo de exemplo
> * Rever pronunciações de ponto final
> * Treinar e publicar o aplicativo
> * Consultar o ponto final da aplicação para ver a resposta JSON de LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar aplicativo de exemplo

Use as etapas a seguir para importar um aplicativo.

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true).

1. No [portal do Luis de visualização](https://preview.luis.ai), importe o arquivo. JSON para um novo aplicativo.

1. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `review`.

    > [!TIP]
    > A clonagem em uma nova versão é uma prática recomendada antes de modificar seu aplicativo. Quando você concluir uma versão, exporte a versão (como um arquivo. JSON ou. Lu) e verifique o arquivo em seu sistema de controle do código-fonte.


1. Para treinar o aplicativo, selecione **treinar**.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicar o aplicativo para acessá-lo do ponto de extremidade HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Adicionar declarações no ponto de extremidade

Neste aplicativo, você tem intenções e entidades, mas não tem nenhum uso de ponto de extremidade. Esse uso de ponto de extremidade é necessário para melhorar o aplicativo com a revisão expressão do ponto de extremidade.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Use o ponto de extremidade para adicionar o seguinte declarações.

    |Expressão do ponto de extremidade|Intenção alinhada|
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

Examine o ponto de extremidade declarações para uma tentativa corretamente alinhada. Embora haja um único pool de declarações para examinar em todas as versões, o processo de alinhamento correto da intenção adiciona o expressão de exemplo somente ao _modelo ativo_ atual.

1. Na seção **Build** do portal, selecione **examinar ponto de extremidade declarações** no painel de navegação esquerdo. A lista está filtrada para a intenção **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de comentário final de comentário botão de](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Este expressão, `I'm looking for a job with Natural Language Processing`, não está na intenção correta.

1.  Para alinhar esse expressão, na linha expressão, selecione a **intenção alinhada** correta de `GetJobInformation`. Adicione o expressão alterado ao aplicativo selecionando a marca de seleção.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de comentário final de comentário botão de](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Examine as declarações restantes nesta tentativa, corrigindo a intenção alinhada conforme necessário. Use a tabela expressão inicial neste tutorial para exibir a intenção alinhada.

    A lista **revisar declarações do ponto de extremidade** não deve mais ter a declarações corrigida. Se mais declarações aparecerem, continue a trabalhar na lista, corrigindo as tentativas alinhadas até que a lista esteja vazia.

    Qualquer correção de rotulação de entidade é feita depois que a intenção é alinhada, na página de detalhes da intenção.

1. Prepare e publique novamente a aplicação.

## <a name="get-intent-prediction-from-endpoint"></a>Obter previsão de intenção do ponto de extremidade

Para verificar se o exemplo alinhado corretamente declarações melhorou a previsão do aplicativo, tente um expressão próximo ao expressão corrigido.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá para o final do URL no endereço e introduza `Are there any natural language processing jobs in my department right now?`. O último parâmetro querystring é `q`, a expressão **query**.

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

   Agora que o incerteza declarações está alinhado corretamente, a intenção correta foi prevista com uma **pontuação alta**.

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
