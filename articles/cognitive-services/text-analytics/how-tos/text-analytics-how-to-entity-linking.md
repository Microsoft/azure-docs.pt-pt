---
title: Utilizar o reconhecimento de entidade com a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como identificar e desambiguar a identidade de uma entidade encontrada em texto com a API text Analytics REST.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/15/2020
ms.author: aahi
ms.openlocfilehash: 9b90f177432de11f8281d03021b38bae647dadf2
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562536"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como utilizar o reconhecimento de entidade nomeada em análise de texto

A API text Analytics permite-lhe tomar texto não estruturado e devolve uma lista de entidades desambiguadas, com links para mais informações na web. A API suporta o reconhecimento de entidades nomeadas (NER) para várias categorias de entidades, e a ligação de entidades.

## <a name="entity-linking"></a>Ligar à Entidade

A ligação da entidade é a capacidade de identificar e desambiguar a identidade de uma entidade encontrada em texto (por exemplo, determinar se uma ocorrência da palavra "Marte" se refere ao planeta, ou ao deus romano da guerra). Este processo requer a presença de uma base de conhecimento numa língua apropriada, para ligar entidades reconhecidas em texto. A Entity Linking usa a [Wikipédia](https://www.wikipedia.org/) como base de conhecimento.

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

Denominado Reconhecimento de Entidade (NER) é a capacidade de identificar diferentes entidades em texto e categorizá-las em classes ou tipos pré-definidos como: pessoa, localização, evento, produto e organização.  

## <a name="personally-identifiable-information-pii"></a>Informação Pessoalmente Identificável (PII)

A funcionalidade PII faz parte do NER e pode identificar e redigir entidades sensíveis em texto que estejam associadas a uma pessoa individual, tais como: número de telefone, endereço de e-mail, endereço de correio eletrónico, número de passaporte.

## <a name="named-entity-recognition-features-and-versions"></a>Características e versões de reconhecimento de entidades nomeadas

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funcionalidade                                                         | NER v3.0 | NER v3.1-pré-visualização.3 |
|-----------------------------------------------------------------|--------|----------|
| Métodos para pedidos individuais e de lote                          | X      | X        |
| Reconhecimento de entidades expandidas em várias categorias           | X      | X        |
| Pontos finais separados para envio de entidades que ligam e pedidos NER. | X      | X        |
| Reconhecimento de entidades de informação pessoais `PII` e de saúde `PHI`        |        | X        |
| Redação de `PII`        |        | X        |

Consulte [o suporte linguístico](../language-support.md) para obter informações.

O Nome De Reconhecimento de Entidade v3 fornece deteção expandida em vários tipos. Atualmente, o NER v3.0 pode reconhecer entidades na [categoria de entidades gerais.](../named-entity-types.md)

Denominado Reconhecimento de Entidade v3.1-pré-visualização.3 inclui as capacidades de deteção de v3.0, e: 
* A capacidade de detetar informações pessoais `PII` () utilizando o `v3.1-preview.3/entities/recognition/pii` ponto final. 
* Um parâmetro opcional `domain=phi` para detetar informações confidenciais de saúde `PHI` ().
* [Operação assíncronea](text-analytics-how-to-call-api.md) utilizando o `/analyze` ponto final.

Para mais informações, consulte o artigo [das categorias de entidades](../named-entity-types.md) e solicite a secção [pontos finais](#request-endpoints) abaixo. 

## <a name="sending-a-rest-api-request"></a>Envio de um pedido de API REST

### <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID, texto, idioma.

Cada documento deve ter menos de 5.120 caracteres e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido.

### <a name="structure-the-request"></a>Estruturar o pedido

Crie um pedido POST. Pode utilizar o [Carteiro](text-analytics-how-to-call-api.md) ou a **consola de testes API** nas seguintes ligações para estruturar e enviar rapidamente uma. 

> [!NOTE]
> Pode encontrar a sua chave e ponto final para o seu recurso Text Analytics no portal azul. Estarão localizados na página **de arranque rápido** do recurso, sob **gestão de recursos.** 


### <a name="request-endpoints"></a>Solicitar pontos finais

#### <a name="version-31-preview3"></a>[Versão 3.1-pré-visualização.3](#tab/version-3-preview)

O Reconhecimento de Entidades `v3.1-preview.3` Nomeadas utiliza pontos finais separados para NER, PII e entidade que ligam pedidos. Utilize um formato URL abaixo com base no seu pedido.

**Ligação de entidades**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[Chamada Entity Recognition versão 3.1-pré-visualização referência para `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**Reconhecimento de Entidades Nomeadas**
* Entidades gerais - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[Chamada Entity Recognition versão 3.1-pré-visualização referência para `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**Informação Pessoalmente Identificável (PII)**
* Informação pessoal `PII` - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

Também pode utilizar o parâmetro opcional `domain=phi` para detetar informações de saúde `PHI` () em texto. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

A partir de `v3.1-preview.3` , a resposta JSON inclui um `redactedText` imóvel, que contém o texto de entrada modificado onde as entidades PII detetadas são substituídas por um `*` por cada personagem nas entidades.

[Chamada Entity Recognition versão 3.1-pré-visualização referência para `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

**Operação assíncronea**

A partir de `v3.1-preview.3` , pode enviar pedidos NER assíncronos utilizando o `/analyze` ponto final.

* Operação assíncronea - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze`

Consulte [como ligar para a API text Analytics](text-analytics-how-to-call-api.md) para obter informações sobre o envio de pedidos assíncronos.

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

O Nome De Reconhecimento de Entidade v3 utiliza pontos finais separados para NER e entidade que liga pedidos. Utilize um formato URL abaixo baseado no seu pedido:

**Ligação de entidades**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Denominada versão de Reconhecimento de Entidade 3.0 para `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Reconhecimento de Entidades Nomeadas**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Denominada versão de Reconhecimento de Entidade 3.0 para `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Desa estatua um cabeçalho de pedido para incluir a sua chave API API de Análise de Texto. No órgão de pedido, forneça os documentos JSON que preparou.

## <a name="example-requests"></a>Pedidos de exemplo

#### <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>Exemplo de pedido de NER sincronizado 

O seguinte JSON é um exemplo de conteúdo que pode enviar para a API. O formato de pedido é o mesmo para ambas as versões da API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>Exemplo assíncronos pedido NER

Se utilizar o `/analyze` ponto final para uma [operação assíncrono,](text-analytics-how-to-call-api.md)obterá uma resposta contendo as tarefas que enviou para a API.

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>Exemplo de pedido de NER sincronizado 

A versão 3.0 inclui apenas uma operação sincronizada. O seguinte JSON é um exemplo de conteúdo que pode enviar para a API. O formato de pedido é o mesmo para ambas as versões da API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>Postar o pedido

A análise é realizada aquando da receção do pedido. Consulte a secção [limite de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de pedidos que pode enviar por minuto e segundo.

O Texto Analytics API é apátrida. Nenhum dado é armazenado na sua conta e os resultados são devolvidos imediatamente na resposta.

## <a name="view-results"></a>Ver resultados

Todos os pedidos post devolvem uma resposta formatada JSON com os IDs e propriedades de entidade detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados. Devido ao suporte multilíngue e emoji, a resposta pode conter compensações de texto. Para obter mais informações, consulte [como processar as compensações de texto.](../concepts/text-offsets.md)

### <a name="example-responses"></a>Respostas de exemplo

A versão 3 fornece pontos finais separados para a ligação geral do NER, PII e entidade. A versão 3.1-pareview inclui um modo de Análise assíncronos. As respostas para estas operações estão abaixo. 

#### <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>Resultados de exemplo sincronizado

Exemplo de uma resposta geral do NER:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

Exemplo de uma resposta PII:

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Exemplo de uma resposta de ligação da Entidade:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>Exemplo resultado assíncronos

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Exemplo de uma resposta geral do NER:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para entidade que liga usando Text Analytics em Serviços Cognitivos. Em resumo:

* Os documentos JSON no órgão de pedido incluem um ID, texto e código linguístico.
* Os pedidos de CORREIO são enviados para um ou mais pontos finais, utilizando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
* A saída de resposta, que consiste em entidades ligadas (incluindo pontuações de confiança, compensações e links web, para cada ID do documento) pode ser utilizada em qualquer aplicação

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
