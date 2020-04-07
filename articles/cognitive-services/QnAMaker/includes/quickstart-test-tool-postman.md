---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: dd44d9cb01ff072d89afeb4efc4a59071c621315
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758694"
---
Este quickstart baseado em Carteiro leva-o através de obter uma resposta da sua base de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* Último [**Carteiro.**](https://www.getpostman.com/)
* Deve ter.
    * Um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Uma base de conhecimento treinada e publicada [com perguntas e respostas construídas](../Quickstarts/add-question-metadata-portal.md) a partir do quickstart é configurada com metadados e chat Chit.

> [!NOTE]
> Quando estiver pronto para gerar uma resposta a uma pergunta da sua base de conhecimentos, deve [treinar](../Quickstarts/create-publish-knowledge-base.md#save-and-train) e [publicar](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a sua base de conhecimentos. Quando a sua base de conhecimentos é publicada, a página **Publicar** apresenta as definições de pedido http para gerar uma resposta. O separador **Carteiro** mostra as definições necessárias para gerar uma resposta.

## <a name="set-up-postman-for-requests"></a>Configurar carteiro para pedidos

Este quickstart utiliza as mesmas configurações para o pedido postman **post** manto e, em seguida, configura para post body JSON enviado para o serviço com base no que você está tentando consultar.

Utilize este procedimento para configurar o Carteiro e, em seguida, leia cada secção subsequente para configurar o corpo POST JSON.

1. A partir da página **Definições** da base de conhecimento, selecione o separador **Carteiro** para ver a configuração utilizada para gerar uma resposta a partir da base de conhecimento. Copie as seguintes informações a utilizar no Carteiro.

    |Nome|Definição|Finalidade e valor|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Este é o método HTTP e a rota para o URL.|
    |`Host`|`https://diberry-qna-s0-s.azurewebsites.net/qnamaker`|Este é o anfitrião da URL. Concatenate os valores do Anfitrião e do Post para obter o URL de resposta gerada completa.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|O valor do cabeçalho para autorizar o seu pedido ao Azure. |
    |`Content-type`|`application/json`|O valor do cabeçalho para o seu conteúdo.|
    ||`{"question":"<Your question>"}`|O corpo do pedido do POST como objeto JSON. Este valor irá alterar-se em cada secção seguinte, dependendo do que a consulta deve fazer.|

1. Abra o Carteiro e crie um novo pedido **post** básico com as definições da base de conhecimento publicada. Nas seguintes secções, altere o corpo POST JSON para alterar a consulta para a sua base de conhecimento.

## <a name="use-metadata-to-filter-answer"></a>Use metadados para filtrar resposta

Num início rápido anterior, os metadados foram adicionados a dois pares QnA para distinguir entre duas questões diferentes. Adicione os metadados à consulta para restringir o filtro apenas ao par QnA relevante.

1. No Carteiro, altere apenas a consulta `strictFilters` JSON adicionando a `service:qna_maker`propriedade com o nome/par de valor de . O corpo JSON deve ser:

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

    A questão é apenas uma palavra, `size`que pode devolver qualquer um dos dois conjuntos de perguntas e respostas. A `strictFilters` matriz diz a resposta `qna_maker` para reduzir apenas para as respostas.

1. A resposta inclui apenas a resposta que satisfaz os critérios do filtro.

    A seguinte resposta foi formatada para a legibilidade:

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

    Se houver um conjunto de perguntas e respostas que não cumpra o prazo de pesquisa mas que tenha cumprido o filtro, não seria devolvido. Em vez disso, a resposta `No good match found in KB.` geral é devolvida.

## <a name="use-debug-query-property"></a>Use propriedade de consulta de depuração

A informação de depuração ajuda-o a entender como a resposta devolvida foi determinada. Embora seja útil, não é necessário. Para gerar uma resposta com informações sobre depuração, adicione a `debug` propriedade:

1. No Carteiro, mude apenas o corpo `debug` JSON adicionando a propriedade. O JSON deve ser:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. A resposta inclui as informações relevantes sobre a resposta. Na seguinte saída JSON, alguns detalhes de depuração foram substituídos por elipse.

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

## <a name="use-test-knowledge-base"></a>Utilizar base de conhecimentos de teste

Se quiser obter uma resposta da base de `isTest` conhecimentos de teste, use a propriedade do corpo.

No Carteiro, mude apenas o corpo `isTest` JSON adicionando a propriedade. O JSON deve ser:

```json
{
    'question':'size',
    'isTest': true
}
```

A resposta jSON usa o mesmo esquema que a consulta de base de conhecimento publicada.

> [!NOTE]
> Se o teste e as bases de conhecimento publicadas forem exatamente iguais, pode ainda haver alguma ligeira variação porque o índice de teste é partilhado entre todas as bases de conhecimento do recurso.

## <a name="query-for-a-chit-chat-answer"></a>Consulta para uma resposta Chit-chat

1. No Carteiro, mude apenas o corpo JSON para uma declaração de final ização de conversação do utilizador. O JSON deve ser:

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

    Uma vez que a pergunta `Thank you` corresponde exatamente a uma pergunta Chit-chat, o Criador de FAQ está completamente confiante com a classificação 100. O QnA Maker também devolveu todas as questões relacionadas, bem como a propriedade de metadados contendo a informação de etiquetade metadados Chit-chat.

## <a name="use-threshold-and-default-answer"></a>Limite de utilização e resposta por defeito

Pode solicitar um limiar mínimo para a resposta. Se o limiar não for cumprido, a resposta por defeito é devolvida.

1. No Carteiro, mude apenas o corpo JSON para uma declaração de final ização de conversação do utilizador. O JSON deve ser:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    A base de conhecimento não deve encontrar essa resposta porque a pontuação da pergunta é de 71%, e em vez disso devolve a resposta padrão que forneceu quando criou a base de conhecimento.

    A resposta devolvida da JSON, incluindo a pontuação e a resposta é:

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

    QnA Maker devolveu `0`uma pontuação de, o que significa não confiança. Também devolveu a resposta padrão.

1. Mude o valor limiar para 60% e solicite novamente a consulta:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    A JSON devolvida encontrou a resposta.

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