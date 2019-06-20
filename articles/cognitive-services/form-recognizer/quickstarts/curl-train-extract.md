---
title: 'Início rápido: Preparar um modelo e extrair dados de formulário com o cURL - reconhecedor de formulário'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, usará a API de REST do reconhecedor de formulário com o cURL para preparar um modelo e extrair dados de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: 9b5f3b77e3af719d0e3b37ac196b1691ce659e5e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271448"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Início rápido: Preparar um modelo de formulário reconhecedor e extrair dados de formulário com a API de REST com cURL

Neste início rápido, usará a API de REST do reconhecedor de formulário do Azure com o cURL para dar formação e Pontuar formulários para extrair os pares chave-valor e tabelas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, tem de ter:
- Aceder à pré-visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à pré-visualização, preencha e envie os [pedido de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) formulário.
- [cURL](https://curl.haxx.se/windows/) instalado.
- Um conjunto de formulários, pelo menos, cinco do mesmo tipo. Irá utilizar estes dados para preparar o modelo. Pode utilizar um [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este início rápido. Carregue os dados para a raiz de uma conta de armazenamento de Blobs do Azure.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do reconhecedor de formulário

Quando são concedido acesso ao utilizar o reconhecedor de formulário, receberá um bem-vindo e-mail com várias ligações e os recursos. Utilize a ligação de "Portal do Azure" nessa mensagem para abrir o portal do Azure e criar um recurso do reconhecedor de formulário. Na **criar** painel, forneça as seguintes informações:

|    |    |
|--|--|
| **Nome** | Um nome descritivo para o seu recurso. Recomendamos que utilize um nome descritivo, por exemplo *MyNameFormRecognizer*. |
| **Subscrição** | Selecione a subscrição do Azure que tenha sido concedida acesso. |
| **Location** | A localização da sua instância de serviço cognitivo. Localizações diferentes podem introduzir a latência, mas não tiver nenhum impacto sobre a disponibilidade de tempo de execução do seu recurso. |
| **Escalão de preço** | O custo do seu recurso depende do escalão de preço que escolher e a sua utilização. Para obter mais informações, consulte a API [os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) que irá conter o seu recurso. Pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

> [!IMPORTANT]
> Normalmente quando criar um recurso de serviço cognitivo no portal do Azure, terá a opção para criar uma chave de assinatura de múltiplos serviços (que é utilizado em vários serviços cognitivos) ou uma chave de subscrição de serviço único (utilizado apenas com um serviço cognitivo específico). No entanto, como o reconhecedor de formulário é uma versão de pré-visualização, não está incluído na subscrição múltiplos serviço, e não é possível criar a subscrição de serviço único, a menos que utilize a ligação fornecida no e-mail de boas-vindos.

Quando o recurso do reconhecedor de forma a conclusão da implantação, localize e selecione-o a partir da **todos os recursos** lista no portal. Em seguida, selecione o **chaves** separador para ver as chaves de subscrição. Nenhuma das chaves fornecerá o acesso a aplicações para o recurso. Copie o valor da **chave 1**. Irá utilizá-lo na próxima seção.

## <a name="train-a-form-recognizer-model"></a>Preparar um modelo de formulário reconhecedor

Em primeiro lugar, terá um conjunto de dados de treinamento num blob de armazenamento do Azure. Deve ter um mínimo de cinco exemplo formulários (documentos PDF e/ou imagens) da mesma tipo/estrutura como seus dados de entrada principais. Em alternativa, pode utilizar um único formulário vazio com dois formulários preenchidos. Nome de ficheiro do formulário vazio tem de incluir a palavra "vazia".

Para preparar um modelo de formulário reconhecedor utilizando os documentos no seu contentor de Blobs do Azure, chame o **treinar** API ao executar o comando cURL que se segue. Antes de executar o comando, efetue estas alterações:

1. Substitua `<Endpoint>` com o ponto final que obteve na sua chave de assinatura do reconhecedor de formulário. Pode encontrá-lo no seu recurso do reconhecedor de formulário **descrição geral** separador.
1. Substitua `<SAS URL>` com um contentor de armazenamento de Blobs do Azure partilhado aceder ao URL de assinatura (SAS) da localização de dados de treinamento. (Obter o URL de SAS ao clicar em "Assinatura de acesso partilhado" no menu de definições na conta de armazenamento e "cadeia de ligação e gerar SAS". Isto irá mostrar o URL de SAS de serviço de Blobs. Ajustar essa url adicionando o containername após .net / e antes? sv = no url, por exemplo:.blob.core.windows.net/ < name_of_your_container > /? sv =... Este é o URL de SAS para ser usado.)
1. Substitua `<subscription key>` com a chave de subscrição que copiou no passo anterior.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Receberá um `200 (Success)` resposta com o seguinte resultado JSON:

```json
{
  "parameters": {
    "Endpoint": "{Endpoint}",
    "Content-Type": "application/json",
    "Ocp-Apim-Subscription-Key": "{API key}",
    "body": {},
    "trainRequest": {
      "source": "/input/data",
      "sourceFilter": {
        "prefix": "",
        "includeSubFolders": false
      }
    }
  },
  "responses": {
    "200": {
      "body": {
        "modelId": "ad1901b6-ddaa-4249-8938-3f03f65cc893",
        "trainingDocuments": [
          {
            "documentName": "0.pdf",
            "pages": 1,
            "errors": [],
            "status": "success"
          },
          {
            "documentName": "1.pdf",
            "pages": 1,
            "errors": [],
            "status": "success"
          },
          {
            "documentName": "2.pdf",
            "pages": 1,
            "errors": [],
            "status": "success"
          },
          {
            "documentName": "3.pdf",
            "pages": 1,
            "errors": [],
            "status": "success"
          },
          {
            "documentName": "4.pdf",
            "pages": 1,
            "errors": [],
            "status": "success"
          }
        ],
        "errors": []
      }
    }
  }
}
```

Tenha em atenção o `"modelId"` valor. Precisará das mesmas nas etapas a seguir.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrair pares chave-valor e tabelas de formulários

Em seguida, irá analisar um documento e extrair dele pares chave-valor e tabelas. Chamar o **modelar - analisar** API ao executar o comando cURL que se segue. Antes de executar o comando, efetue estas alterações:

1. Substitua `<Endpoint>` com o ponto final que obteve na sua chave de assinatura do reconhecedor de formulário. Pode encontrá-lo no seu recurso do reconhecedor de formulário **descrição geral** separador.
1. Substitua `<modelID>` com o ID de modelo que recebeu na secção anterior.
1. Substitua `<path to your form>` com o caminho do seu formulário (por exemplo, C:\temp\file.pdf).
1. Substitua `<file type>` com o tipo de ficheiro. Tipos suportados: pdf, imagem/jpeg, png/imagem.
1. Substitua `<subscription key>` pela sua chave de subscrição.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Examinar a resposta

Uma resposta de êxito é devolvida em JSON. Ele representa os pares chave-valor e as tabelas extraídas do formulário:

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

Neste início rápido, utilizou a API de REST do reconhecedor de formulário com o cURL para preparar um modelo e executá-lo num cenário de exemplo. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de forma mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da REST API](https://aka.ms/form-recognizer/api)
