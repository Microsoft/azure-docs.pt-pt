---
title: 'Início rápido: treinar um modelo e extrair dados de formulário usando o reconhecedor de forma de ondulação'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a API REST do reconhecedor de formulário com ondulação para treinar um modelo e extrair dados de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: pafarley
ms.openlocfilehash: 97e7341fa48bbd381bb193dcb1436e32564bb591
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473984"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Início rápido: treinar um modelo de reconhecimento de formulário e extrair dados de formulário usando a API REST com ondulação

Neste guia de início rápido, você usará a API REST do reconhecedor do Azure Form com a rotação para treinar e pontuar formulários para extrair pares de chave-valor e tabelas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Este guia de início rápido usa a API do Forms Recognizer v 1.0. Se sua assinatura estiver na região de `West US 2` ou `West Europe`, você deverá seguir o[início rápido da API v 2,0](./curl-train-extract.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido, você deve ter:
- Acesso à visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à visualização, preencha e envie o formulário [solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) .
- [rotação](https://curl.haxx.se/windows/) instalada.
- Um conjunto de pelo menos cinco formulários do mesmo tipo. Você usará esses dados para treinar o modelo. Você pode usar um [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este guia de início rápido. Carregue os arquivos de treinamento na raiz de um contêiner de armazenamento de BLOBs em uma conta de armazenamento do Azure.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Treinar um modelo de reconhecimento de formulário

Primeiro, você precisará de um conjunto de dados de treinamento em um blob de armazenamento do Azure. Você deve ter um mínimo de cinco formulários preenchidos (documentos PDF e/ou imagens) do mesmo tipo/estrutura que os dados de entrada principais. Ou, você pode usar um único Formulário vazio com dois formulários preenchidos. O nome do arquivo do formulário vazio precisa incluir a palavra "Empty". Consulte [criar um conjunto de dados de treinamento para um modelo personalizado](../build-training-data-set.md) para obter dicas e opções para reunir seus dados de treinamento.

Para treinar um modelo de reconhecimento de formulário com os documentos em seu contêiner de blob do Azure, chame a API de **treinamento** executando o comando de rotação a seguir. Antes de executar o comando, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido com a assinatura do reconhecedor de formulário.
1. Substitua `<subscription key>` pela chave de assinatura que você copiou da etapa anterior.
1. Substitua `<SAS URL>` pela URL da assinatura de acesso compartilhado (SAS) do contêiner de armazenamento de BLOBs do Azure. Para recuperar a URL SAS, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no contêiner e selecione **obter assinatura de acesso compartilhado**. Verifique se as permissões de **leitura** e **lista** estão marcadas e clique em **criar**. Em seguida, copie o valor na seção **URL** . Ele deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Você receberá uma resposta `200 (Success)` com a seguinte saída JSON:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Observe o valor de `"modelId"`. Você precisará dela nas etapas a seguir.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrair pares de chave-valor e tabelas de formulários

Em seguida, você analisará um documento e extrairá pares de chave-valor e tabelas dele. Chame a API **Model-Analyze** executando o comando de ondulação a seguir. Antes de executar o comando, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido da sua assinatura do reconhecedor de formulário.
1. Substitua `<modelID>` pela ID do modelo que você recebeu na seção anterior.
1. Substitua `<path to your form>` pelo caminho do arquivo do formulário (por exemplo, C:\temp\File.pdf). Para este guia de início rápido, você pode usar os arquivos na pasta **Test** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Substitua `<file type>` pelo tipo de arquivo. Tipos com suporte: `application/pdf`, `image/jpeg`, `image/png`.
1. Substitua `<subscription key>` pela sua chave de subscrição.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Examinar a resposta

Uma resposta de êxito é retornada em JSON. Ele representa os pares de chave-valor e as tabelas extraídas do formulário:

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você usou a API REST do reconhecedor de formulário com ondulação para treinar um modelo e executá-lo em um cenário de exemplo. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de formulário mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)