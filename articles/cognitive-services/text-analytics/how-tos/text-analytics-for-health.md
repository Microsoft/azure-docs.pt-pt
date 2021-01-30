---
title: Como usar o Text Analytics para a saúde
titleSuffix: Azure Cognitive Services
description: Saiba como extrair e rotular informações médicas de texto clínico não estruturado com Text Analytics para a saúde.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: d7f71afd1cf47ab1a94d984090e8fb52ee69c6b4
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99088943"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Como: Utilizar o Text Analytics para a saúde (pré-visualização)

> [!IMPORTANT] 
> Text Analytics for health é uma capacidade de pré-visualização fornecida "AS IS" e "WITH ALL FAULTS". Como tal, **o Text Analytics for health (pré-visualização) não deve ser implementado ou implantado em qualquer utilização de produção.** Texto Analytics para a saúde não é destinado ou disponibilizado para uso como dispositivo médico, suporte clínico, ferramenta de diagnóstico ou outra tecnologia destinada a ser usada no diagnóstico, cura, mitigação, tratamento ou prevenção de doenças ou outras condições, e nenhuma licença ou direito é concedido pela Microsoft para usar esta capacidade para tais fins. Esta capacidade não é concebida ou destina-se a ser implementada ou implementada como um substituto de aconselhamento médico profissional ou de opinião de cuidados de saúde, diagnóstico, tratamento ou julgamento clínico de um profissional de saúde, e não deve ser usada como tal. O cliente é o único responsável por qualquer utilização de Text Analytics para a saúde. A Microsoft não garante que o Text Analytics para a saúde ou quaisquer materiais fornecidos em conexão com a capacidade seja suficiente para qualquer finalidade médica ou de outra forma satisfaça os requisitos de saúde ou médico de qualquer pessoa. 


Text Analytics for health é uma característica do serviço de API text Analytics que extrai e rotula informações médicas relevantes de textos não estruturados, tais como notas de médico, resumos de descarga, documentos clínicos e registos de saúde eletrónicos.  Existem duas formas de utilizar este serviço: 

* [A API baseada na web (assíncrono)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Um recipiente Docker (sincronizado)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Funcionalidades

Text Analytics for health realiza Reconhecimento de Entidade Nomeada (NER), extração de relação, negação de entidade e entidade que liga texto em inglês para descobrir insights em texto clínico e biomédico não estruturado.

### <a name="named-entity-recognition"></a>[Reconhecimento de Entidades Nomeadas](#tab/ner)

O Reconhecimento de Entidades com nome deteta palavras e frases mencionadas em texto não estruturado que podem ser associadas a um ou mais tipos semânticos, tais como diagnóstico, nome da medicação, sintoma/sinal ou idade.

> [!div class="mx-imgBorder"]
> ![NER de Saúde](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Extração de relação](#tab/relation-extraction)

A extração de relação identifica ligações significativas entre conceitos mencionados em texto. Por exemplo, uma relação de "tempo de condição" é encontrada associando um nome de condição com uma hora. 

> [!div class="mx-imgBorder"]
> ![Saúde RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Ligar à Entidade](#tab/entity-linking)

Entidade Que Liga entidades distintas associando entidades nomeadas mencionadas em texto a conceitos encontrados numa base de dados pré-indefinida de conceitos. Por exemplo, o Sistema Unificado de Língua Médica (UMLS).

> [!div class="mx-imgBorder"]
> ![Saúde EL](../media/ta-for-health/health-entity-linking.png)

Texto Analytics para suportes de saúde ligados aos vocabulários biomédicos e de saúde encontrados no Sistema De Língua Médica Unificada[(UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metathesaurus Knowledge Source.

### <a name="negation-detection"></a>[Deteção de Negação](#tab/negation-detection) 

O significado do conteúdo médico é altamente afetado por modificadores como a negação, que pode ter implicações críticas se for diagnosticado erradamente. Texto Analytics para saúde suporta a deteção de negação para as diferentes entidades mencionadas no texto. 

> [!div class="mx-imgBorder"]
> ![Saúde NEG](../media/ta-for-health/health-negation.png)

---

Consulte as [categorias de entidades devolvidas](../named-entity-types.md?tabs=health) pela Text Analytics para obter uma lista completa de entidades apoiadas. Para obter informações sobre as pontuações de confiança, consulte a [nota de transparência text Analytics](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Línguas e regiões apoiadas

O Texto Analytics para a saúde só suporta documentos em inglês. 

O Texto Analytics para a Saúde hospedado web API está atualmente disponível apenas nestas regiões: West US 2, East US 2, Central US, North Europe e West Europe.

## <a name="request-access-to-the-public-preview"></a>Solicitar acesso à pré-visualização pública

Preencha e envie o formulário de [pedido de Serviços Cognitivos](https://aka.ms/csgate) para solicitar o acesso ao Texto Analytics para pré-visualização do público em saúde. Não será cobrado para Text Analytics para uso de saúde. 

O formulário solicita informações sobre si, sobre a sua empresa e sobre o cenário de utilizador para o qual utilizará o recipiente. Depois de submeter o formulário, a equipa dos Serviços Cognitivos da Azure irá analisá-lo e enviar-lhe um e-mail com uma decisão.

> [!IMPORTANT]
> * No formulário, deve utilizar um endereço de e-mail associado a um ID de assinatura Azure.
> * O recurso Azure que utiliza deve ter sido criado com o ID de assinatura Azure aprovado. 
> * Consulte o seu e-mail (pastas de caixa de entrada e lixo) para obter atualizações sobre o estado da sua aplicação da Microsoft.

## <a name="using-the-docker-container"></a>Usando o recipiente Docker 

Para executar o Text Analytics para o recipiente de saúde no seu próprio ambiente, siga estas [instruções para descarregar e instalar o recipiente](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Utilizar a biblioteca de cliente

O mais recente pré-relanço da biblioteca de clientes Text Analytics permite-lhe ligar para o Text Analytics para saúde utilizando um objeto de cliente. Consulte a documentação de referência e veja os exemplos no GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Envio de um pedido de API REST 

### <a name="preparation"></a>Preparação

Texto Analytics for health produz um resultado de maior qualidade quando você dá-lhe quantidades menores de texto para trabalhar. Isto é oposto a algumas das outras funcionalidades do Text Analytics, tais como a extração de frases-chave que funciona melhor em blocos maiores de texto. Para obter os melhores resultados destas operações, considere a reestruturação dos inputs em conformidade.

Você deve ter documentos JSON neste formato: ID, texto e idioma. 

O tamanho do documento deve ser inferior a 5.120 caracteres por documento. Para o número máximo de documentos permitidos numa recolha, consulte o artigo [limite de dados](../concepts/data-limits.md?tabs=version-3) nos Termos. A coleção é enviada no corpo do pedido.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Estruturar o pedido da API para a API web assíncrono hospedado

Tanto para o recipiente como para a API web hospedada, deve criar um pedido DEM. Você pode [usar o Carteiro](text-analytics-how-to-call-api.md), um comando cURL ou a **consola de testes API** no [Text Analytics para a saúde hospedada referência API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) para construir rapidamente e enviar um pedido DEM para a API web hospedada na região desejada. 

Abaixo está um exemplo de um ficheiro JSON anexado ao Text Analytics para o corpo POSTAL do pedido de API de saúde:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Resposta assíncrono da API web assíncrono 

Uma vez que este pedido DE POST é usado para submeter um trabalho para a operação assíncronea, não há texto no objeto de resposta.  No entanto, precisa do valor da CHAVE de localização de operação nos cabeçalhos de resposta para fazer um pedido GET para verificar o estado do trabalho e a saída.  Abaixo está um exemplo do valor da CHAVE de localização de operação no cabeçalho de resposta do pedido DO POST:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Para verificar o estado do trabalho, faça um pedido GET ao URL no valor do cabeçalho CHAVE de localização de operação da resposta POST.  Os seguintes Estados são utilizados para refletir o estatuto de trabalho: `NotStarted` , , , , , e `running` `succeeded` `failed` `rejected` `cancelling` `cancelled` .  

Pode cancelar um trabalho com um `NotStarted` ou estado com uma chamada DELETE HTTP para o mesmo URL que o pedido `running` GET.  Mais informações sobre a chamada DELETE estão disponíveis no [Text Analytics para referência de API hospedada em saúde](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Segue-se um exemplo da resposta de um pedido GET.  Por favor, note que a saída está disponível para recuperação até que a `expirationDateTime` (24 horas a partir do momento em que o trabalho foi criado) tenha passado após o que a saída é purgada.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Estruturar o pedido da API para o contentor

Pode utilizar o [Carteiro](text-analytics-how-to-call-api.md) ou o pedido de cURL de exemplo abaixo para submeter uma consulta ao contentor que implementou, substituindo a `serverURL` variável pelo valor adequado.  Note que a versão da API no URL para o recipiente é diferente da API hospedada.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

O seguinte JSON é um exemplo de um ficheiro JSON anexado ao Text Analytics para o corpo POSTAL do pedido de API de saúde:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Corpo de resposta ao recipiente

O seguinte JSON é um exemplo do Text Analytics para o organismo de resposta da API de saúde a partir da chamada sincronizada contentorizada:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Saída de deteção de negação

Ao utilizar a deteção de negação, em alguns casos, um único termo de negação pode abordar vários termos ao mesmo tempo. A negação de uma entidade reconhecida está representada na produção JSON pelo valor booleano da `isNegated` bandeira, por exemplo:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Saída de extração de relação

A saída de extração de relação contém referências URI à *origem* da relação, e o seu *alvo*. As entidades com o papel de relação `ENTITY` são atribuídas ao `target` campo. As entidades com o papel de relação `ATTRIBUTE` são atribuídas ao `source` campo. As relações de abreviatura contêm campos e campos bidirecionais, `source` `target` e `bidirectional` serão definidas para `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Ver também

* [Descrição geral da Análise de Texto](../overview.md)
* [Categorias de entidades nomeadas](../named-entity-types.md)
* [Novidades](../whats-new.md)
