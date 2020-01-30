---
title: 'Início rápido: treinar um modelo e extrair dados de formulário usando o reconhecedor de forma de ondulação'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a API REST do reconhecedor de formulário com ondulação para treinar um modelo e extrair dados de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 961a5cc3d8c90007e314f40ba98693d978fe8888
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771953"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Início rápido: treinar um modelo de reconhecimento de formulário e extrair dados de formulário usando a API REST com ondulação

Neste guia de início rápido, você usará a API REST do reconhecedor do Azure Form com a rotação para treinar e pontuar formulários para extrair pares de chave-valor e tabelas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Este guia de início rápido usa a API do Forms Recognizer v 2.0. Se sua assinatura não estiver na região `West US 2` ou `West Europe`, você precisará usar a API v 1.0. Em vez disso, siga o guia de [início rápido v 1.0](./curl-train-extract-v1.md) .

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você deve ter:
- Acesso à visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à visualização, preencha e envie o formulário [solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) .
- [rotação](https://curl.haxx.se/windows/) instalada.
- Um conjunto de pelo menos seis formas do mesmo tipo. Você usará cinco deles para treinar o modelo e, em seguida, irá testá-lo com o sexto formato. Seus formulários podem ser de diferentes tipos de arquivo, mas devem ser do mesmo tipo de documento. Você pode usar um [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este guia de início rápido. Carregue os arquivos de treinamento na raiz de um contêiner de armazenamento de BLOBs em uma conta de armazenamento do Azure. Você pode colocar os arquivos de teste em uma pasta separada.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Treinar um modelo de reconhecimento de formulário

Primeiro, você precisará de um conjunto de dados de treinamento em um blob de armazenamento do Azure. Você deve ter um mínimo de cinco formulários preenchidos (documentos PDF e/ou imagens) do mesmo tipo/estrutura que os dados de entrada principais. Ou, você pode usar um único Formulário vazio com dois formulários preenchidos. O nome do arquivo do formulário vazio precisa incluir a palavra "Empty". Consulte construir um conjunto de dados de [treino para um modelo personalizado](../build-training-data-set.md) para dicas e opções para reunir os seus dados de treino.

> [!NOTE]
> Você pode usar o recurso de dados rotulados para rotular manualmente alguns ou todos os seus dados de treinamento com antecedência. Esse é um processo mais complexo, mas resulta em um modelo melhor treinado. Consulte a seção [treinar com rótulos](../overview.md#train-with-labels) da visão geral para saber mais sobre esse recurso.

Para treinar um modelo de reconhecimento de formulário com os documentos em seu contêiner de BLOBs do Azure, chame a API de **[modelo personalizado Training](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** executando o comando de rotação a seguir. Antes de executar o comando, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido com a assinatura do reconhecedor de formulário.
1. Substitua `<subscription key>` pela chave de assinatura que você copiou da etapa anterior.
1. Substitua `<SAS URL>` pela URL da assinatura de acesso compartilhado (SAS) do contêiner de armazenamento de BLOBs do Azure. Para recuperar a URL SAS, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no contêiner e selecione **obter assinatura de acesso compartilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter a forma: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Você receberá uma resposta de `201 (Success)` com um cabeçalho de **local** . O valor desse cabeçalho é a ID do novo modelo que está sendo treinado. 

## <a name="get-training-results"></a>Obter resultados de treinamento

Depois de iniciar a operação de treinamento, você usará uma nova operação, **[obterá o modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** para verificar o status de treinamento. Passe a ID do modelo para esta chamada à API para verificar o status de treinamento:

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido com a chave de assinatura do reconhecedor de formulário.
1. Substitua `<subscription key>` pela sua chave de assinatura
1. Substitua `<model ID>` pela ID do modelo que você recebeu na etapa anterior

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Você receberá uma resposta `200 (Success)` com um corpo JSON no formato a seguir. Observe o campo `"status"`. Isso terá o valor `"ready"` quando o treinamento for concluído. Se o modelo não tiver terminado o treinamento, você precisará consultar o serviço novamente executando o comando novamente. É recomendável um intervalo de um segundo ou mais entre as chamadas.

O campo `"modelId"` contém a ID do modelo que você está treinando. Você precisará disso para a próxima etapa.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analisar formulários para pares de chave-valor e tabelas

Em seguida, você usará seu modelo treinado recentemente para analisar um documento e extrair pares de chave-valor e tabelas dele. Chame a API de **[análise de formulário](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** executando o comando de rotação a seguir. Antes de executar o comando, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido da chave de assinatura do reconhecedor de formulário. Você pode encontrá-lo na guia **visão geral** de recursos do reconhecedor de formulário.
1. Substitua `<model ID>` pela ID do modelo que você recebeu na seção anterior.
1. Substitua `<SAS URL>` por uma URL SAS em seu arquivo no armazenamento do Azure. Siga as etapas na seção de treinamento, mas, em vez de obter uma URL SAS para todo o contêiner de BLOB, obtenha um para o arquivo específico que você deseja analisar.
1. Substitua `<subscription key>` pela sua chave de subscrição.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Você receberá uma resposta de `202 (Success)` com um cabeçalho **de local de operação** . O valor desse cabeçalho inclui uma ID de resultados que você usa para acompanhar os resultados da operação de análise. Salve essa ID de resultados para a próxima etapa.

## <a name="get-the-analyze-results"></a>Obter os resultados da análise

Use a API a seguir para consultar os resultados da operação de análise.

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido da chave de assinatura do reconhecedor de formulário. Você pode encontrá-lo na guia **visão geral** de recursos do reconhecedor de formulário.
1. Substitua `<result ID>` pela ID que você recebeu na seção anterior.
1. Substitua `<subscription key>` pela sua chave de subscrição.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Você receberá uma resposta `200 (Success)` com um corpo JSON no formato a seguir. A saída foi reduzida para simplificar a simplicidade. Observe o campo `"status"` próximo à parte inferior. Isso terá o valor `"succeeded"` quando a operação de análise for concluída. Se a operação de análise não for concluída, você precisará consultar o serviço novamente executando o comando novamente. É recomendável um intervalo de um segundo ou mais entre as chamadas.

As associações e as tabelas principais do par chave/valor estão no nó `"pageResults"`. Se você também especificou a extração de texto sem formatação por meio do parâmetro URL *includeTextDetails* , o nó `"readResults"` mostrará o conteúdo e as posições de todo o texto no documento.

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

Neste guia de início rápido, você usou a API REST do reconhecedor de formulário com ondulação para treinar um modelo e executá-lo em um cenário de exemplo. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de formulário mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
