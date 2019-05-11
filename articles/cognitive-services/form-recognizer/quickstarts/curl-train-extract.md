---
title: 'Início rápido: Preparar um modelo e extrair dados de formulário com o cURL - reconhecedor de formulário'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá utilizar a API de REST do reconhecedor de formulário com o cURL para preparar um modelo e extrair dados de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: f5c87457f5d19b107f5722bc8c6a95174555332a
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546355"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-curl"></a>Início rápido: Preparar um modelo de formulário reconhecedor e extrair dados de formulário com a REST API com cURL

Neste início rápido, irá utilizar a API de REST do reconhecedor de formulário com o cURL para dar formação e Pontuar formulários para extrair os pares chave-valor e tabelas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Obteve acesso à pré-visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à pré-visualização, preencha e envie os [pedido de acesso do reconhecedor de formulário de serviços cognitivos](https://aka.ms/FormRecognizerRequestAccess) formulário.
- Tem de ter o [cURL](https://curl.haxx.se/windows/).
- Tem de ter uma chave de subscrição para o reconhecedor de formulário. Siga as instruções de subscrição de serviço único em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#single-service-subscription) para subscrever o reconhecedor de formulário e obtenha a chave. Não utilize a subscrição de múltiplos serviço, como isso não incluirá o serviço do reconhecedor de formulário.
- Tem de ter um conjunto mínimo de cinco formulários do mesmo tipo. Pode utilizar um [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este início rápido.

## <a name="train-a-form-recognizer-model"></a>Preparar um modelo de formulário reconhecedor

Em primeiro lugar, terá um conjunto de dados de treinamento. Pode utilizar dados num Blob do Azure ou os seus dados de treinamento locais. Deve ter um mínimo de cinco exemplo formulários (documentos PDF e/ou imagens) da mesma tipo/estrutura como seus dados de entrada principais. Em alternativa, pode utilizar um único formulário vazio; nome de ficheiro do formulário inclui a palavra "vazia".

Para preparar um modelo de formulário reconhecedor usando os documentos no seu contentor de Blobs do Azure, chame o **treinar** API executando o comando cURL abaixo. Antes de executar o comando, efetue as seguintes alterações:

* Substitua `<Endpoint>` com o ponto final que obteve da sua chave de assinatura do reconhecedor de formulário. Pode encontrá-lo no seu separador de descrição geral de recursos do reconhecedor de formulário.
* Substitua `<SAS URL>` com um contentor de armazenamento de Blobs do Azure partilhado aceder ao URL de assinatura (SAS), onde os dados de treinamento estão localizados.  
* Substitua `<subscription key>` pela sua chave de subscrição.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Receberá um `200 (Success)` resposta com o seguinte resultado JSON:

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

Anote o `"modelId"` valor; irá precisar dele para obter os passos seguintes.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrair pares chave-valor e tabelas de formulários

Em seguida, irá analisar um documento e extrair dele pares chave-valor e tabelas. Chamar o **modelar - analisar** API executando o comando cURL abaixo. Antes de executar o comando, efetue as seguintes alterações:

* Substitua `<Endpoint>` com o ponto final que obteve da sua chave de assinatura do reconhecedor de formulário. Pode encontrá-lo no seu recurso do reconhecedor de formulário **descrição geral** separador.
* Substitua `<modelID>` com o ID de modelo que recebeu no passo anterior de treinar o modelo.
* Substitua `<path to your form>` com o caminho do ficheiro ao seu formulário.
* Substitua `<subscription key>` pela sua chave de subscrição.
* Substitua `<file type>` com o tipo de ficheiro - tipos com suporte de pdf, imagem/jpeg, png/imagem.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida é devolvida em JSON e representa as tabelas e pares de chave-valor extraídos do formulário.

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

## <a name="next-steps"></a>Passos Seguintes

Neste guia, utilizou as APIs de REST do reconhecedor de formulário com o cURL para preparar um modelo e executá-lo num caso de exemplo. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de forma mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da REST API](https://aka.ms/form-recognizer/api)
