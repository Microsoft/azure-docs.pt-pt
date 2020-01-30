---
title: Usar o reconhecimento de entidade com o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como identificar e desambiguar a identidade de uma entidade encontrada em texto com a API REST do Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/27/2019
ms.author: aahi
ms.openlocfilehash: 9aa00898c6a567d495ed0c66bcf7bd475067fa0d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774145"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como usar o reconhecimento de entidade nomeada no Análise de Texto

O API de Análise de Texto permite que você assuma o texto não estruturado e retorne uma lista de entidades desambiguadas, com links para mais informações na Web. A API dá suporte ao reconhecimento de entidade nomeada (NER) e à vinculação de entidade.

### <a name="entity-linking"></a>Ligação de Entidades

A vinculação de entidades é a capacidade de identificar e desambiguar a identidade de uma entidade encontrada em texto (por exemplo, determinar se uma ocorrência da palavra `Mars` refere-se ao planeta ou ao Deus romano de guerra). Esse processo requer a presença de uma base de dados de conhecimento em um idioma apropriado para vincular entidades reconhecidas em texto. A Entidade Que Liga a Wikipédia usa a [Wikipédia](https://www.wikipedia.org/) como base de conhecimento.


### <a name="named-entity-recognition-ner"></a>Reconhecimento de entidade nomeada (NER)

O NER (reconhecimento de entidade nomeada) é a capacidade de identificar diferentes entidades no texto e categorizá-las em classes ou tipos predefinidos. Por exemplo: pessoas, lugares e organizações.

## <a name="named-entity-recognition-versions-and-features"></a>Versões e recursos de reconhecimento de entidade nomeada

O API de Análise de Texto oferece duas versões do reconhecimento de entidade nomeada-V2 e v3. A versão 3 (visualização pública) fornece um maior detalhe nas entidades que podem ser detectadas e categorizadas.

| Funcionalidade                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Métodos para solicitações únicas e em lote                          | X      | X      |
| Reconhecimento de entidade básica em várias categorias              | X      | X      |
| Classificação expandida para entidades reconhecidas                 |        | X      |
| Separe os pontos de extremidade para enviar solicitações de vinculação e NER de entidade. |        | X      |
| Controle de versão de modelo                                                |        | X      |

Consulte [suporte a idiomas](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) para obter informações.

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

### <a name="entity-types"></a>Tipos de entidade

> [!NOTE]
> O NER (reconhecimento de entidade nomeada) versão 2 só dá suporte às entidades a seguir. O NER v3 está em visualização pública e expande muito o número e a profundidade das entidades reconhecidas no texto.   

| Tipo  | Subtipo | Exemplo |
|:-----------   |:------------- |:---------|
| Pessoa        | \* N/A         | "Jeff", "Bill Gates"     |
| Localização      | \* N/A         | "Redmond, Washington", "Paris"  |
| Organização  | \* N/A         | "Microsoft"   |
| Quantidade      | Número        | "6", "seis"     |
| Quantidade      | Percentagem    | "50%", "cinquenta por cento"|
| Quantidade      | Ordinal       | "2.º", "segundo"     |
| Quantidade      | Idade           | "dia de 90 dias antigos", "30 anos de idade"    |
| Quantidade      | Moeda      | "10,99 $"     |
| Quantidade      | Dimensão     | "10 milhas", "40 cm"     |
| Quantidade      | Temperatura   | "32 graus"    |
| DateTime      | \* N/A         | "18:30 4 de fevereiro de 2012"      |
| DateTime      | Date          | "2 de maio de 2017", "02/05/2017"   |
| DateTime      | Tempo          | "8:00", "8:00"  |
| DateTime      | DateRange     | "2 a 5 de maio"    |
| DateTime      | TimeRange     | "18:00 às 19:00"     |
| DateTime      | Duração      | "1 minuto e 45 segundos"   |
| DateTime      | Definir           | "todas as terças"     |
| URL           | \* N/A         | "https:\//www.bing.com"    |
| E-mail         | \* N/A         | "support@contoso.com" |
| Número de telefone dos EUA  | \* N/A         | (Somente números de telefone dos EUA) "(312) 555-0176" |
| Endereço IP    | \* N/A         | "10.0.0.100" |

\* dependendo das entidades de entrada e extraídas, determinadas entidades podem omitir a `SubType`.  Todos os tipos de entidade com suporte listados estão disponíveis apenas para idiomas inglês, chinês simplificado, francês, alemão e espanhol.

### <a name="request-endpoints"></a>Pontos de extremidade de solicitação

O reconhecimento de entidade nomeada v2 usa um único ponto de extremidade para NER e solicitações de vinculação de entidade:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (visualização pública)](#tab/version-3)

### <a name="entity-types"></a>Tipos de entidade

O reconhecimento de entidade nomeada v3 fornece detecção expandida entre vários tipos. Atualmente, o NER v3 pode reconhecer as seguintes categorias de entidades:

* Geral
* Informações Pessoais 

Para obter uma lista detalhada de entidades e linguagens com suporte, consulte o artigo [tipos de entidade com suporte do Ner v3](../named-entity-types.md) .

### <a name="request-endpoints"></a>Pontos de extremidade de solicitação

O reconhecimento de entidade nomeada v3 usa pontos de extremidade separados para solicitações de vinculação de NER e entidade. Use um formato de URL abaixo com base em sua solicitação:

NER
* Entidades gerais-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Informações pessoais-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Vinculação de entidade
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Controle de versão de modelo

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

---

## <a name="sending-a-rest-api-request"></a>Enviando uma solicitação da API REST

### <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID, texto, idioma.

Cada documento deve ter menos de 5.120 caracteres, e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido.

### <a name="structure-the-request"></a>Estruturar a solicitação

Crie uma solicitação POST. Você pode [usar o postmaster](text-analytics-how-to-call-api.md) ou o **console de teste de API** nos links a seguir para estruturar e enviar rapidamente um. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

[Referência v2 de reconhecimento de entidade nomeada (NER)](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

A versão 2 usa o ponto de extremidade a seguir para solicitações de vinculação de entidade e NER: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

#### <a name="version-3tabversion-3"></a>[Versão 3](#tab/version-3)

[Referência v3 de reconhecimento de entidade nomeada](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

A versão 3 usa pontos de extremidade separados para solicitações de vinculação de NER e entidade. Use um formato de URL abaixo com base em sua solicitação:

NER
* Entidades gerais-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entidades de informações pessoais-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Vinculação de entidade
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

---

Defina um cabeçalho de solicitação para incluir sua chave de API de Análise de Texto. No corpo da solicitação, forneça os documentos JSON que você preparou.

### <a name="example-ner-request"></a>Exemplo de solicitação NER 

Veja a seguir um exemplo de conteúdo que você pode enviar para a API. O formato da solicitação é o mesmo para as duas versões da API.

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

## <a name="post-the-request"></a>Postar a solicitação

A análise é realizada aquando da receção do pedido. Consulte a seção [limites de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo.

O API de Análise de Texto é sem monitoração de estado. Nenhum dado é armazenado em sua conta e os resultados são retornados imediatamente na resposta.

## <a name="view-results"></a>Ver resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades de entidade detectadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

### <a name="example-ner-v2-response"></a>Exemplo de resposta do NER v2
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

#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (visualização pública)](#tab/version-3)

### <a name="example-v3-responses"></a>Exemplo de respostas v3

A versão 3 fornece pontos de extremidade separados para NER e vinculação de entidade. As respostas para ambas as operações estão abaixo.

#### <a name="example-ner-response"></a>Exemplo de resposta NER

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

#### <a name="example-entity-linking-response"></a>Exemplo de resposta de vinculação de entidade

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

---

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para vinculação de entidade usando Análise de Texto em serviços cognitivas. Em resumo:

* O reconhecimento de entidade nomeada está disponível para os idiomas selecionados em duas versões.
* Os documentos JSON no corpo da solicitação incluem uma ID, um texto e um código de idioma.
* As solicitações POST são enviadas para um ou mais pontos de extremidade, usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para sua assinatura.
* A saída de resposta, que consiste em entidades vinculadas (incluindo pontuações de confiança, deslocamentos e links da Web, para cada ID de documento) pode ser usada em qualquer aplicativo

## <a name="next-steps"></a>Passos seguintes

* [Descrição Geral da Análise de Texto](../overview.md)
* [Usando a biblioteca de cliente Análise de Texto](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
