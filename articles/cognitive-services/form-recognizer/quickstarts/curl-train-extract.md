---
title: 'Quickstart: Treine um modelo e extrai a partir de cURL - Reconhecimento de Formulário'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilizará o Formulário Recogniser REST API com cURL para treinar um modelo e extrair dados de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 32756187852de0834afc1dc034d3f7419f0c8087
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118403"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Quickstart: Treine um modelo de reconhecimento de formulário e extrai dados de formulário utilizando a API REST com cURL

Neste arranque rápido, utilizará a API REST API do Reconhecimento de Formulários Azure com cURL para treinar e marcar formulários para extrair pares e tabelas de valor-chave.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, deve ter:
- [cURL](https://curl.haxx.se/windows/) instalado.
- Um conjunto de pelo menos seis formas do mesmo tipo. Vais usar cinco destes para treinar o modelo, e depois vais testá-lo com a sexta forma. Os seus formulários podem ser de diferentes tipos de ficheiros, mas devem ser o mesmo tipo de documento. Pode utilizar um conjunto de dados de [amostra](https://go.microsoft.com/fwlink/?linkid=2090451) para este arranque rápido. Faça upload dos ficheiros de treino para a raiz de um recipiente de armazenamento blob numa conta de Armazenamento Azure. Pode colocar os ficheiros de teste numa pasta separada.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Treine um modelo de reconhecimento de formulário

Primeiro, vai precisar de um conjunto de dados de treino numa bolha de armazenamento Azure. Deve ter um mínimo de cinco formulários preenchidos (documentos PDF e/ou imagens) do mesmo tipo/estrutura que os seus principais dados de entrada. Ou, pode usar um único formulário vazio com dois formulários preenchidos. O nome do ficheiro vazio precisa incluir a palavra "vazio". Consulte construir um conjunto de dados de [treino para um modelo personalizado](../build-training-data-set.md) para dicas e opções para reunir os seus dados de treino.

> [!NOTE]
> Pode utilizar previamente a função de dados etiquetada para rotular manualmente alguns ou todos os seus dados de treino. Este é um processo mais complexo, mas resulta num modelo mais bem treinado. Consulte o Comboio com a secção de [etiquetas](../overview.md#train-with-labels) da visão geral para saber mais sobre esta funcionalidade.

Para treinar um modelo 'Reconhecimento de Formulários' com os documentos no seu recipiente de blob Azure, ligue para o **[Modelo Personalizado do Comboio](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** API executando o seguinte comando cURL. Antes de executar o comando, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto final que obteve com a subscrição do Reconhecimento de Formulários.
1. Substitua `<subscription key>` com a chave de subscrição que copiou do passo anterior.
1. Substitua `<SAS URL>` com o URL de assinatura de acesso partilhado (SAS) do recipiente de armazenamento Azure Blob. Para recuperar o URL SAS, abra o Microsoft Azure Storage Explorer, clique no seu recipiente e selecione Obter assinatura de **acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter a forma: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Receberá uma resposta `201 (Success)` com um cabeçalho de **localização.** O valor deste cabeçalho é a identificação do novo modelo que está a ser treinado. 

## <a name="get-training-results"></a>Obtenha resultados de formação

Depois de iniciar a operação do comboio, usa uma nova operação, **[Get Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** para verificar o estado de treino. Passe o ID do modelo para esta chamada da API para verificar o estado de formação:

1. Substitua `<Endpoint>` pelo ponto final que obteve com a chave de subscrição do Reconhecimento de Formulários.
1. Substitua `<subscription key>` com a sua chave de subscrição
1. Substitua `<model ID>` pelo modelo de IDENTIFICAÇÃO que recebeu no passo anterior

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Receberá uma resposta `200 (Success)` com um corpo JSON no seguinte formato. Reparem no campo `"status"`. Isto terá o valor `"ready"` assim que o treino estiver concluído. Se o modelo não terminar o treino, terá de consultar o serviço novamente reexecutando o comando. Recomendamos um intervalo de um segundo ou mais entre chamadas.

O campo `"modelId"` contém a identificação do modelo que estás a treinar. Vai precisar disto para o próximo passo.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analisar formulários para pares e tabelas de valor-chave

Em seguida, você usará o seu modelo recém-treinado para analisar um documento e extrair pares e tabelas de valor-chave do mesmo. Ligue para a **[API do Formulário de Análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** executando o seguinte comando cURL. Antes de executar o comando, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto final que obteve da sua chave de subscrição 'Reconhecimento de Formulários'. Pode encontrá-lo no separador de **visão geral** do recurso 'Reconhecimento de Formulários'.
1. Substitua `<model ID>` pelo ID modelo que recebeu na secção anterior.
1. Substitua `<SAS URL>` por um URL SAS no seu ficheiro no armazenamento Azure. Siga os passos na secção de Formação, mas em vez de obter um URL SAS para todo o recipiente de bolhas, obtenha um para o ficheiro específico que pretende analisar.
1. Substitua `<subscription key>` pela sua chave de subscrição.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Receberá uma resposta `202 (Success)` com um cabeçalho de **Localização de Operação.** O valor deste cabeçalho inclui um ID de resultados que utiliza para acompanhar os resultados da operação Analisar. Guarde esta identificação dos resultados para o próximo passo.

## <a name="get-the-analyze-results"></a>Obtenha os resultados da Análise

Utilize a Seguinte API para consultar os resultados da operação Analisar.

1. Substitua `<Endpoint>` pelo ponto final que obteve da sua chave de subscrição 'Reconhecimento de Formulários'. Pode encontrá-lo no separador de **visão geral** do recurso 'Reconhecimento de Formulários'.
1. Substitua `<result ID>` com o ID que recebeu na secção anterior.
1. Substitua `<subscription key>` pela sua chave de subscrição.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Receberá uma resposta `200 (Success)` com um corpo JSON no seguinte formato. A saída foi encurtada para a simplicidade. Repare maquete `"status"` campo perto do fundo. Isto terá o valor `"succeeded"` quando a operação Analisar estiver concluída. Se a operação Analyze não tiver terminado, terá de consultar novamente o serviço reexecutando o comando. Recomendamos um intervalo de um segundo ou mais entre chamadas.

As principais associações e tabelas de pares chave/valor estão no nó `"pageResults"`. Se também especificou a extração de texto simples através do parâmetro *URL do TextDetails,* então o nó `"readResults"` mostrará o conteúdo e as posições de todo o texto no documento.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Melhorar os resultados

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, utilizou o Formulário Recogniser REST API com cURL para treinar um modelo e executá-lo num cenário de amostragem. Em seguida, consulte a documentação de referência para explorar a API do Reconhecimento de Formulários com mais profundidade.

> [!div class="nextstepaction"]
> [Documento de referência rest API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
