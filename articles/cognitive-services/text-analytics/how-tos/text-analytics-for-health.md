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
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599440"
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

A extração de relação identifica ligações significativas entre conceitos mencionados em texto. Por exemplo, uma relação de "tempo de condição" é encontrada associando um nome de condição com um tempo ou entre uma abreviatura e a descrição completa.  

> [!div class="mx-imgBorder"]
> ![Saúde RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Ligar à Entidade](#tab/entity-linking)

Entidade Que Liga entidades distintas associando entidades nomeadas mencionadas em texto a conceitos encontrados numa base de dados pré-indefinida de conceitos, incluindo o Sistema Unificado de Línguas Médicas (UMLS). Os conceitos médicos também são atribuídos como nome preferencial, como uma forma adicional de normalização.

> [!div class="mx-imgBorder"]
> ![Saúde EL](../media/ta-for-health/health-entity-linking.png)

Texto Analytics para suportes de saúde ligados aos vocabulários biomédicos e de saúde encontrados no Sistema De Língua Médica Unificada[(UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metathesaurus Knowledge Source.

### <a name="assertion-detection"></a>[Deteção de Afirmação](#tab/assertion-detection) 

O significado do conteúdo médico é altamente afetado por modificadores, tais como afirmações negativas ou condicionais que podem ter implicações críticas se forem mal representadas. Text Analytics for health suporta três categorias de deteção de afirmação para entidades no texto: 

* certeza
* condicional
* associação

> [!div class="mx-imgBorder"]
> ![Saúde NEG](../media/ta-for-health/assertions.png)

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

> [!NOTE]
> Tanto os pontos assíncronos `/analyze` como `/health` os pontos finais só estão disponíveis nas seguintes regiões: West US 2, East US 2, Central US, North Europe e West Europe.  Para fazer pedidos bem sucedidos a estes pontos finais, certifique-se de que o seu recurso é criado numa destas regiões.

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

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

Para verificar o estado do trabalho, faça um pedido GET ao URL no valor do cabeçalho CHAVE de localização de operação da resposta POST.  Os seguintes Estados são utilizados para refletir o estatuto de trabalho: `NotStarted` , , , , , e `running` `succeeded` `failed` `rejected` `cancelling` `cancelled` .  

Pode cancelar um trabalho com um `NotStarted` ou estado com uma chamada DELETE HTTP para o mesmo URL que o pedido `running` GET.  Mais informações sobre a chamada DELETE estão disponíveis no [Text Analytics para referência de API hospedada em saúde](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Segue-se um exemplo da resposta de um pedido GET.  A saída está disponível para recuperação até que a `expirationDateTime` (24 horas a partir do momento em que o trabalho foi criado) tenha passado após o que a saída é purgada.

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
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
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
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
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Saída de afirmação

Text Analytics for health returns assertion modifiers, que são atributos informativos atribuídos a conceitos médicos que proporcionam uma compreensão mais profunda do contexto dos conceitos dentro do texto. Estes modificadores são divididos em três categorias, cada uma focando-se num aspeto diferente, e contendo um conjunto de valores mutuamente exclusivos. Apenas um valor por categoria é atribuído a cada entidade. O valor mais comum para cada categoria é o valor Padrão. A resposta de saída do serviço contém apenas modificadores de afirmação que são diferentes do valor padrão.

**CERTAINTY**  – fornece informações sobre a presença (presente vs. ausente) do conceito e a certeza de que o texto tem a ver com a sua presença (definitivamente vs. possível).
*   **Positivo** [Padrão]: o conceito existe ou aconteceu.
* **Negativo:** o conceito não existe agora ou nunca aconteceu.
* **Positive_Possible:** o conceito provavelmente existe, mas há alguma incerteza.
* **Negative_Possible:** a existência do conceito é improvável, mas há alguma incerteza.
* **Neutral_Possible:** o conceito pode ou não existir sem uma tendência para qualquer um dos lados.

**CONDICIONALIDADE –** fornece informações sobre se a existência de um conceito depende de determinadas condições. 
*   **Nenhum** [Padrão]: o conceito é um facto e não hipotético e não depende de determinadas condições.
*   **Hipotética:** o conceito pode desenvolver-se ou ocorrer no futuro.
*   **Condicional:** o conceito existe ou ocorre apenas sob determinadas condições.

**ASSOCIAÇÃO** – descreve se o conceito está associado ao tema do texto ou a outra pessoa.
*   **Assunto** [Predefinição]: o conceito está associado ao tema do texto, normalmente o paciente.
*   **Someone_Else:** o conceito está associado a alguém que não é o tema do texto.


A deteção de afirmação representa entidades negadas como um valor negativo para a categoria de certeza, por exemplo:

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty": "negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id": "0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>Saída de extração de relação

O Texto Analytics for Health reconhece as relações entre diferentes conceitos, incluindo as relações entre atributo e entidade (por exemplo, direção da estrutura corporal, dosagem de medicação) e entre entidades (por exemplo, deteção de abreviaturas).

**ABREVIATURA**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * As relações referentes à CONDIÇÃO podem referir-se ao tipo de entidade de DIAGNÓSTICO ou ao tipo de entidade SYMPTOM_OR_SIGN.
> * As relações referentes à MEDICAÇÃO podem referir-se ao tipo de entidade MEDICATION_NAME ou ao tipo de entidade MEDICATION_CLASS.
> * As relações referentes ao TIME podem referir-se ao tipo de entidade TIME ou ao tipo de entidade DATA.

A saída de extração de relação contém referências URI e funções atribuídas às entidades do tipo de relação. Por exemplo:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>Ver também

* [Descrição geral da Análise de Texto](../overview.md)
* [Categorias de entidades nomeadas](../named-entity-types.md)
* [Novidades](../whats-new.md)
