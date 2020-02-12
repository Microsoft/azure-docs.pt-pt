---
title: Utilize o reconhecimento de entidade com a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba identificar e desambiguar a identidade de uma entidade encontrada em texto com a API REST Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 607b65d6a6893901ce23cd48c277c14209128866
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137977"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como utilizar o Reconhecimento de EntidadeS Nomeadas em Análise de Texto

A API text Analytics permite-lhe obter texto não estruturado e devolve uma lista de entidades desambiguadas, com links para mais informações na web. A API apoia tanto o reconhecimento de entidades nomeadas (NER) como a ligação de entidades.

### <a name="entity-linking"></a>Ligar à Entidade

A ligação da entidade é a capacidade de identificar e desambigar a identidade de uma entidade encontrada em texto (por exemplo, determinando se uma ocorrência da palavra `Mars` se refere ao planeta, ou ao deus romano da guerra). Este processo requer a presença de uma base de conhecimento numa linguagem apropriada, para ligar entidades reconhecidas em texto. A Entidade Que Liga a Wikipédia usa a [Wikipédia](https://www.wikipedia.org/) como base de conhecimento.


### <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeada (NER)

Denominada Reconhecimento de Entidades (NER) é a capacidade de identificar diferentes entidades em texto e categorizá-las em classes ou tipos pré-definidos. Por exemplo: pessoas, lugares e organizações.

## <a name="named-entity-recognition-versions-and-features"></a>Versões e funcionalidades de Reconhecimento de Entidades nomeadas

O Text Analytics API oferece duas versões de Reconhecimento de Entidades Nomeadas - v2 e v3. A versão 3 (pré-visualização pública) fornece um maior detalhe nas entidades que podem ser detetadas e categorizadas.

| Funcionalidade                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Métodos para pedidos individuais e de lote                          | X      | X      |
| Reconhecimento de entidades básicas em várias categorias              | X      | X      |
| Classificação alargada para entidades reconhecidas                 |        | X      |
| Pontos finais separados para envio de entidades que liguem e solicitam NER. |        | X      |
| Versão do modelo                                                |        | X      |

Consulte o [suporte linguístico](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) para obter informações.


#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

### <a name="entity-types"></a>Tipos de entidade

O chamado Reconhecimento de Entidades v3 fornece deteção expandida em vários tipos. Atualmente, ner v3 pode reconhecer as seguintes categorias de entidades:

* Geral
* Informações Pessoais 

Para obter uma lista detalhada de entidades e línguas apoiadas, consulte o artigo de [entidades apoiadas pelo NER v3.](../named-entity-types.md)

### <a name="request-endpoints"></a>Pontos finais de pedido

O chamado Reconhecimento de Entidades v3 utiliza pontos finais separados para pedidos de NER e entidades que ligam. Utilize um formato URL abaixo com base no seu pedido:

NER
* Entidades gerais - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Informações pessoais - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Ligação de entidades
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Versão do modelo

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

### <a name="entity-types"></a>Tipos de entidade

> [!NOTE]
> Denominada Versão 2 de Reconhecimento de Entidades (NER) suporta apenas as seguintes entidades. O NER v3 está em pré-visualização pública, e amplia consideravelmente o número e a profundidade das entidades reconhecidas em texto.   

| Tipo  | SubTipo | Exemplo |
|:-----------   |:------------- |:---------|
| Pessoa        | N/A\*         | "Jeff", "Bill Gates"     |
| Localização      | N/A\*         | "Redmond, Washington", "Paris"  |
| Organização  | N/A\*         | "Microsoft"   |
| Quantidade      | Número        | "6", "six"     |
| Quantidade      | Percentagem    | "50%", "cinquenta por cento"|
| Quantidade      | Ordinal       | "2º", "segundo"     |
| Quantidade      | Idade           | "90 dias de idade", "30 anos"    |
| Quantidade      | Moeda      | "$10,99"     |
| Quantidade      | Dimensão     | "10 milhas", "40 cm"     |
| Quantidade      | Temperatura   | "32 graus"    |
| DateTime      | N/A\*         | "18:30 H 4 de fevereiro de 2012"      |
| DateTime      | Data          | "2 de maio de 2017", "05/02/2017"   |
| DateTime      | Hora          | "8:00", "8:00"  |
| DateTime      | DataRange     | "2 de maio a 5 de maio"    |
| DateTime      | Intervalo de tempo     | "18h00 às 19h"     |
| DateTime      | Duração      | "1 minuto e 45 segundos"   |
| DateTime      | Definir           | "todas as terças-feiras"     |
| do IdP           | N/A\*         | "https:\//www.bing.com"    |
| Email         | N/A\*         | "support@contoso.com" |
| Número de telefone dos EUA  | N/A\*         | (apenas números de telefone dos EUA) "(312) 555-0176" |
| Endereço IP    | N/A\*         | "10.0.0.100" |

\* Dependendo da entrada e das entidades extraídas, algumas entidades podem omiti-lo `SubType`.  Todos os tipos de entidades apoiadas listados estão disponíveis apenas para as línguas inglesa, chinesa- simplificada, francesa, alemã e espanhola.

### <a name="request-endpoints"></a>Pontos finais de pedido

O Denominado Reconhecimento de Entidades v2 utiliza um único ponto final para pedidos de ligação ner e entidade:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Envio de um pedido de API de REPOUSO

### <a name="preparation"></a>Preparação

Deve ter documentos JSON neste formato: ID, texto, idioma.

Cada documento deve ter menos de 5.120 caracteres, podendo ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido.

### <a name="structure-the-request"></a>Estruturar o pedido

Crie um pedido post. Pode [utilizar](text-analytics-how-to-call-api.md) o Carteiro ou a consola de **teste API** nos seguintes links para estruturar e enviar uma. 

> [!NOTE]
> Pode encontrar a sua chave e ponto final para o seu recurso Text Analytics no portal azul. Eles estarão localizados na página de **arranque rápida** do recurso, sob gestão de **recursos.** 

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[Referência de Reconhecimento de Entidade seleção v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

A versão 3 utiliza pontos finais separados para pedidos de NER e entidades que ligam. Utilize um formato URL abaixo com base no seu pedido:

NER
* Entidades gerais - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entidades de informação pessoal - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Ligação de entidades
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

[Referência de Reconhecimento de Entidade (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

A versão 2 utiliza o seguinte ponto final para a ligação de entidades e pedidos de NER: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Detete um cabeçalho de pedido para incluir a sua chave API de Análise de Texto. No órgão de pedido, forneça os documentos da JSON que preparou.

### <a name="example-ner-request"></a>Pedido de NER exemplo 

Segue-se um exemplo de conteúdo que pode enviar para a API. O formato de pedido é o mesmo para ambas as versões da API.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>Poste o pedido

A análise é realizada aquando da receção do pedido. Consulte a secção de [limites](../overview.md#data-limits) de dados na visão geral para obter informações sobre o tamanho e número de pedidos que pode enviar por minuto e segundo.

A API de Análise de Texto é apátrida. Não são armazenados dados na sua conta e os resultados são devolvidos imediatamente na resposta.

## <a name="view-results"></a>Ver resultados

Todos os pedidos post devolvem uma resposta formatada JSON com as IDs e propriedades de entidades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.


#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização)](#tab/version-3)

### <a name="example-v3-responses"></a>Respostas exemplo v3

A versão 3 fornece pontos finais separados para ner e ligação de entidades. As respostas para ambas as operações estão abaixo.

#### <a name="example-ner-response"></a>Resposta NER exemplo

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Exemplo de entidade que liga resposta

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Resposta ner v2
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para entidades que ligam o Texto Analytics em Serviços Cognitivos. Em resumo:

* O Reconhecimento de Entidades está disponível para idiomas selecionados em duas versões.
* Os documentos da JSON no organismo de pedido incluem um código de identificação, texto e idioma.
* Os pedidos POST são enviados para um ou mais pontos finais, utilizando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
* A saída de resposta, que consiste em entidades ligadas (incluindo pontuações de confiança, compensações e ligações web, para cada id de documento) pode ser utilizada em qualquer aplicação

## <a name="next-steps"></a>Passos seguintes

* [Descrição Geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
