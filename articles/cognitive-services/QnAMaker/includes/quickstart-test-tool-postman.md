---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386660"
---
Este quickstart baseado no Carteiro leva-o a obter uma resposta da sua base de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* Último [**Carteiro.**](https://www.getpostman.com/)
* Deve ter.
    * Um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Uma base de conhecimento treinada e publicada [com perguntas e respostas construídas](../Quickstarts/add-question-metadata-portal.md) a partir do quickstart é configurada com metadados e chit chat.

> [!NOTE]
> Quando estiver pronto para gerar uma resposta a uma pergunta da sua base de conhecimento, deve [treinar](../Quickstarts/create-publish-knowledge-base.md#save-and-train) e [publicar](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a sua base de conhecimentos. Quando a sua base de conhecimentos é publicada, a página **Publicar** apresenta as definições de pedido HTTP para gerar uma resposta. O **separador Carteiro** mostra as definições necessárias para gerar uma resposta.

## <a name="set-up-postman-for-requests"></a>Configurar carteiro para pedidos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Este quickstart utiliza as mesmas definições para o pedido do Postman **POST** e, em seguida, configura para o corpo POST JSON enviado para o serviço com base no que você está tentando consultar.

Utilize este procedimento para configurar o Carteiro e, em seguida, leia cada secção subsequente para configurar o corpo POST JSON.

1. A partir da página **Definições** da base de conhecimento, selecione o **separador Carteiro** para ver a configuração utilizada para gerar uma resposta a partir da base de conhecimento. Copie as seguintes informações para usar no Carteiro.

    |Name|Definição|Finalidade e valor|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Este é o método HTTP e rota para o URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|Este é o anfitrião da URL. Concatenate os valores de Anfitrião e Post para obter o URL completo de 200% gerado.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|O valor do cabeçalho para autorizar o seu pedido ao Azure. |
    |`Content-type`|`application/json`|O valor do cabeçalho para o seu conteúdo.|
    ||`{"question":"<Your question>"}`|O corpo do pedido do POST como objeto JSON. Este valor mudará em cada secção seguinte, dependendo do que a consulta deve fazer.|

1. Abra o Carteiro e crie um novo pedido básico **de POST** com as definições de base de conhecimento publicadas. Nas seguintes secções, altere o corpo DOM JSON para alterar a consulta para a sua base de conhecimento.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Este quickstart utiliza as mesmas definições para o pedido do Postman **POST** e, em seguida, configura para o corpo POST JSON enviado para o serviço com base no que você está tentando consultar.

Utilize este procedimento para configurar o Carteiro e, em seguida, leia cada secção subsequente para configurar o corpo POST JSON.

1. A partir da página **Definições** da base de conhecimento, selecione o **separador Carteiro** para ver a configuração utilizada para gerar uma resposta a partir da base de conhecimento. Copie as seguintes informações para usar no Carteiro.

    |Name|Definição|Finalidade e valor|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Este é o método HTTP e rota para o URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|Este é o anfitrião da URL. Concatenate os valores de Anfitrião e Post para obter o URL completo de 200% gerado.|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|O valor do cabeçalho para autorizar o seu pedido. |
    |`Content-type`|`application/json`|O valor do cabeçalho para o seu conteúdo.|
    ||`{"question":"<Your question>"}`|O corpo do pedido do POST como objeto JSON. Este valor mudará em cada secção seguinte, dependendo do que a consulta deve fazer.|

1. Abra o Carteiro e crie um novo pedido básico **de POST** com as definições de base de conhecimento publicadas. Nas seguintes secções, altere o corpo DOM JSON para alterar a consulta para a sua base de conhecimento.
---

## <a name="use-metadata-to-filter-answer"></a>Use metadados para filtrar a resposta

Num início rápido anterior, os metadados foram adicionados a dois pares QnA para distinguir entre duas questões diferentes. Adicione os metadados à consulta para restringir o filtro apenas ao par QnA relevante.

1. No Carteiro, altere apenas a consulta JSON adicionando a `strictFilters` propriedade com o nome/par de valor de `service:qna_maker` . O corpo JSON deve ser:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    A questão é apenas uma palavra, `size` que pode devolver qualquer uma das duas perguntas e responder a pares. A `strictFilters` matriz diz a resposta para reduzir apenas às `qna_maker` respostas.

1. A resposta inclui apenas a resposta que satisfaz os critérios do filtro.

    Foi formatada a seguinte resposta para a legibilidade:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Se houver um par de perguntas e respostas que não cumpriu o termo de pesquisa mas que cumpriu o filtro, não seria devolvido. Em vez disso, a resposta geral `No good match found in KB.` é devolvida.

## <a name="use-debug-query-property"></a>Use propriedade de consulta de depurg

> [!NOTE]
>Não recomendamos usar a propriedade Debug para qualquer dependência. Esta propriedade foi adicionada para ajudar a equipa de produtos na resolução de problemas.

A informação de depurg ajuda-o a entender como a resposta devolvida foi determinada. Embora seja útil, não é necessário. Para gerar uma resposta com informações de depurg, adicione a `debug` propriedade:

1. No Carteiro, mude apenas o corpo JSON adicionando a `debug` propriedade. O JSON deve ser:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. A resposta inclui a informação relevante sobre a resposta. Na seguinte saída JSON, alguns detalhes de depurg foram substituídos por elipse.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Utilize a base de conhecimentos de teste

Se quiser obter uma resposta da base de conhecimentos de teste, use a propriedade do `isTest` corpo.

No Carteiro, mude apenas o corpo JSON adicionando a `isTest` propriedade. O JSON deve ser:

```json
{
    'question':'size',
    'isTest': true
}
```

A resposta JSON usa o mesmo esquema que a consulta de base de conhecimento publicada.

> [!NOTE]
> Se o teste e as bases de conhecimento publicadas forem exatamente as mesmas, pode ainda haver uma ligeira variação porque o índice de teste é partilhado entre todas as bases de conhecimento no recurso.

## <a name="query-for-a-chit-chat-answer"></a>Consulta para uma resposta chit-chat

1. No Carteiro, mude apenas o corpo JSON para uma declaração final de conversa do utilizador. O JSON deve ser:

    ```json
    {
        'question':'thank you'
    }
    ```

1. A resposta inclui a pontuação e a resposta.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Uma vez que a pergunta `Thank you` corresponde exatamente a uma pergunta Chit-chat, o Criador de FAQ está completamente confiante com a classificação 100. O QnA Maker também devolveu todas as questões relacionadas, bem como a propriedade de metadados que contém a informação da etiqueta de metadados chit-chat.

## <a name="use-threshold-and-default-answer"></a>Utilizar limiar e resposta predefinida

Pode solicitar um limiar mínimo para a resposta. Se o limiar não for atingido, a resposta por defeito é devolvida.

1. No Carteiro, mude apenas o corpo JSON para uma declaração final de conversa do utilizador. O JSON deve ser:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    A base de conhecimento não deve encontrar essa resposta porque a pontuação da pergunta é de 71%, e em vez disso devolva a resposta padrão que forneceu quando criou a base de conhecimento.

    A resposta devolvida ao JSON, incluindo a pontuação e a resposta é:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker devolveu uma pontuação de `0` , o que significa que não há confiança. Também devolveu a resposta por defeito.

1. Altere o valor limiar para 60% e solicite novamente a consulta:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    O JSON devolvido encontrou a resposta.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```