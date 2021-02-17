---
title: Cartões de visita - Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a análise do cartão de visita com a API do Reconhecimento de Formulários - utilização e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: c2543f74b90205a36d3f5b4481beca35c779f77e
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546028"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Modelo de cartões de visita pré-construídos do Reconhecimento de Formulários 

O Azure Form Recogniser pode analisar e extrair informações de contacto de cartões de visita utilizando o seu modelo de cartões de visita pré-construídos. Combina poderosas capacidades de reconhecimento de caracteres óticos (OCR) com o nosso modelo de compreensão de cartões de visita para extrair informações chave dos cartões de visita em inglês. Extrai informações pessoais de contacto, nome da empresa, cargo, e muito mais. A API do Cartão de Visita Pré-Construído está disponível publicamente na pré-visualização do Formulário Reconhecedor v2.1. 

## <a name="what-does-the-business-card-service-do"></a>O que faz o serviço de Cartão de Visita?

A API do Cartão de Visita pré-construído extrai campos-chave dos cartões de visita e devolve-os numa resposta organizada da JSON.

![Imagem itemada de Contoso da saída FOTT + JSON](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Campos extraídos:

|Nome| Tipo | Descrição | Texto | 
|:-----|:----|:----|:----|
| Nomes de contacto | matriz de objetos | Nome de contacto extraído do cartão de visita | [{ "Primeiro Nome": "John", "LastName": "Doe" }] |
| FirstName | string | Primeiro (dado) nome de contacto | "João" | 
| LastName | string | Último (família) nome de contacto |     "Doe" | 
| Nomes de empresa | matriz de cadeias (de carateres) | Nome da empresa extraído do cartão de visita | ["Contoso"] | 
| Departamentos | matriz de cadeias (de carateres) | Departamento ou organização de contacto | ["R&D"] | 
| JobTles | matriz de cadeias (de carateres) | Título de contato de emprego listado | ["Engenheiro de Software"] | 
| E-mails | matriz de cadeias (de carateres) | E-mail de contato extraído do cartão de visita | ["johndoe@contoso.com"] | 
| Sites | matriz de cadeias (de carateres) | Site extraído do cartão de visita | ["https://www.contoso.com"] | 
| Endereços | matriz de cadeias (de carateres) | Endereço extraído do cartão de visita | ["123 Main Street, Redmond, WA 98052"] | 
| Telemóveis | matriz de números de telefone | Número de telemóvel extraído do cartão de visita | ["+19876543210"] |
| Faxes | matriz de números de telefone | Número de telefone de fax extraído do cartão de visita | ["+19876543211"] |
| Telefones de trabalho | matriz de números de telefone | Número de telefone de trabalho extraído do cartão de visita | ["+19876543231"] |
| Outros Telefones     | matriz de números de telefone | Outro número de telefone extraído do cartão de visita | ["+19876543233"] |


A API do Cartão de Visita também pode devolver todo o texto reconhecido do Cartão de Visita. Esta saída de OCR está incluída na resposta JSON.  

### <a name="input-requirements"></a>Requisitos de entrada 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>A operação do Cartão de Visita analisar

O [Cartão De Negócios analisar](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) tira uma imagem ou PDF de um cartão de visita como entrada e extrai os valores de interesse. A chamada devolve um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o Resultado ID para ser usado no passo seguinte.

|Cabeçalho de resposta| URL de resultados |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>A operação Get Analyze Cartão De visita

O segundo passo é chamar a operação [Get Analyze Business Card Result.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult) Esta operação toma como entrada o Resultado ID que foi criado pela operação Do Cartão De Negócios de Análise. Devolve uma resposta JSON que contém um campo **de estado** com os seguintes valores possíveis. Você chama a esta operação iterativamente até que ela retorne com o valor **bem sucedido.** Utilize um intervalo de 3 a 5 segundos para evitar exceder os pedidos por segundo (RPS).

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | notStarted: A operação de análise ainda não começou.<br /><br />funcionamento: A operação de análise está em curso.<br /><br />falhou: A operação de análise falhou.<br /><br />conseguiu: A operação de análise foi bem sucedida.|

Quando o campo **de status** tiver o valor **bem sucedido,** a resposta JSON incluirá o entendimento do cartão de visita e os resultados de reconhecimento de texto opcional, se solicitado. O resultado do entendimento do cartão de visita é organizado como um dicionário de valores de campo nomeados, onde cada valor contém o texto extraído, valor normalizado, caixa de delimitação, confiança e elementos de palavra correspondentes. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com texto, caixa de delimitação e informação de confiança.

![amostra de saída de cartão de visita](./media/business-card-results.png)

### <a name="sample-json-output"></a>Amostra de saída JSON

A resposta à operação Get Analyze Cartão De visita Resulte será a representação estruturada do cartão de visita com toda a informação extraída.  Consulte aqui um [ficheiro de cartão de visita de amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) e a sua saída de [cartão de visita](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)de amostra de saída estruturada .

Veja o seguinte exemplo de uma resposta JSON bem sucedida:
* O `"readResults"` nó contém todo o texto reconhecido. O texto é organizado por página, depois por linha, depois por palavras individuais. 
* O `"documentResults"` nó contém os valores específicos do cartão de visita que o modelo descobriu. É aqui que encontrará informações úteis de contacto como o primeiro nome, apelido, nome da empresa e muito mais.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Siga o [quickstart quickstart](./QuickStarts/client-library.md) para implementar a extração de dados do cartão de visita utilizando python e a API REST.

## <a name="customer-scenarios"></a>Cenários do Cliente  

Os dados extraídos com a API do Cartão de Visita podem ser utilizados para executar uma variedade de tarefas. Extrair esta informação de contacto economiza automaticamente tempo para aqueles em funções viradas para o cliente. Seguem-se alguns exemplos do que os nossos clientes conseguiram com a API do Cartão de Visita:

* Extrair informações de contacto de cartões de visita e criar rapidamente contactos telefónicos. 
* Integre com CRM para criar automaticamente contacto usando imagens de cartão de visita. 
* Mantenha-se a saber das pistas de vendas.  
* Extrair informações de contacto a granel a partir de imagens de cartão de visita existentes. 

A API do Cartão De Visita também alimenta a [funcionalidade de Processamento de Cartões de Visita AI Builder.](/ai-builder/prebuilt-business-card)

## <a name="next-steps"></a>Passos seguintes

- Siga o [quickstart](./quickstarts/client-library.md) para começar a reconhecer cartões de visita.

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](./overview.md)
* [REST API referenciar docs](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
