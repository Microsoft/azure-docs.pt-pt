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
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 457be5ac014fda6b4984ed7af3dcc89780b16379
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141622"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como utilizar o reconhecimento de entidade nomeada em análise de texto

A API text Analytics permite-lhe tomar texto não estruturado e devolve uma lista de entidades desambiguadas, com links para mais informações na web. A API suporta o reconhecimento de entidades nomeadas (NER) e a ligação de entidades.

### <a name="entity-linking"></a>Ligar à Entidade

A ligação da entidade é a capacidade de identificar e desambiguar a identidade de uma entidade encontrada em texto (por exemplo, determinar se uma ocorrência da palavra "Marte" se refere ao planeta, ou ao deus romano da guerra). Este processo requer a presença de uma base de conhecimento numa língua apropriada, para ligar entidades reconhecidas em texto. A Entity Linking usa a [Wikipédia](https://www.wikipedia.org/) como base de conhecimento.


### <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

Denominado Reconhecimento de Entidade (NER) é a capacidade de identificar diferentes entidades em texto e categorizá-las em classes ou tipos pré-definidos como: pessoa, localização, evento, produto e organização.  

## <a name="named-entity-recognition-versions-and-features"></a>Versões e funcionalidades de Reconhecimento de Entidades nomeadas

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funcionalidade                                                         | NER v3.0 | NER v3.1-pré-visualização.1 |
|-----------------------------------------------------------------|--------|----------|
| Métodos para pedidos individuais e de lote                          | X      | X        |
| Reconhecimento de entidades expandidas em várias categorias           | X      | X        |
| Pontos finais separados para envio de entidades que ligam e pedidos NER. | X      | X        |
| Reconhecimento de entidades de informação pessoais `PII` e de saúde `PHI`        |        | X        |

Consulte [o suporte linguístico](../language-support.md) para obter informações.

### <a name="entity-types"></a>Tipos de entidade

O Nome De Reconhecimento de Entidade v3 fornece deteção expandida em vários tipos. Atualmente, o NER v3.0 pode reconhecer entidades na [categoria de entidades gerais.](../named-entity-types.md)

O Nome De Reconhecimento de Entidade v3.1-preview.1 inclui as capacidades de deteção de v3.0, e a capacidade de detetar informações pessoais ( `PII` ) usando o `v3.1-preview.1/entities/recognition/pii` ponto final. Pode utilizar o parâmetro opcional `domain=phi` para detetar informações confidenciais de saúde `PHI` (). Consulte o artigo [das categorias de entidades](../named-entity-types.md) e solicite a secção [de pontos finais](#request-endpoints) abaixo para obter mais informações.


## <a name="sending-a-rest-api-request"></a>Envio de um pedido de API REST

### <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID, texto, idioma.

Cada documento deve ter menos de 5.120 caracteres e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido.

### <a name="structure-the-request"></a>Estruturar o pedido

Crie um pedido POST. Pode utilizar o [Carteiro](text-analytics-how-to-call-api.md) ou a **consola de testes API** nas seguintes ligações para estruturar e enviar rapidamente uma. 

> [!NOTE]
> Pode encontrar a sua chave e ponto final para o seu recurso Text Analytics no portal azul. Estarão localizados na página **de arranque rápido** do recurso, sob **gestão de recursos.** 


### <a name="request-endpoints"></a>Solicitar pontos finais

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

O Nome De Reconhecimento de Entidade v3 utiliza pontos finais separados para NER e entidade que liga pedidos. Utilize um formato URL abaixo baseado no seu pedido:

Ligação de entidades
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

#### <a name="version-31-preview1"></a>[Versão 3.1-pré-visualização.1](#tab/version-3-preview)

O Reconhecimento de Entidades `v3.1-preview.1` Nomeadas utiliza pontos finais separados para NER e entidade que ligam pedidos. Utilize um formato URL abaixo baseado no seu pedido:

Ligação de entidades
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/linking`

NER
* Entidades gerais -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/general`

* Informação pessoal `PII` -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii`

Também pode utilizar o parâmetro opcional `domain=phi` para detetar informações de saúde `PHI` () em texto. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi`

---

Desa estatua um cabeçalho de pedido para incluir a sua chave API API de Análise de Texto. No órgão de pedido, forneça os documentos JSON que preparou.

### <a name="example-ner-request"></a>Exemplo NER pedido 

Segue-se um exemplo de conteúdo que poderá enviar para a API. O formato de pedido é o mesmo para ambas as versões da API.

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

## <a name="post-the-request"></a>Postar o pedido

A análise é realizada aquando da receção do pedido. Consulte a secção [limite de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de pedidos que pode enviar por minuto e segundo.

O Texto Analytics API é apátrida. Nenhum dado é armazenado na sua conta e os resultados são devolvidos imediatamente na resposta.

## <a name="view-results"></a>Ver resultados

Todos os pedidos post devolvem uma resposta formatada JSON com os IDs e propriedades de entidade detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados. Devido ao suporte multilíngue e emoji, a resposta pode conter compensações de texto. Veja [como processar as compensações de texto](../concepts/text-offsets.md) para obter mais informações.

### <a name="example-v3-responses"></a>Exemplo v3 respostas

A versão 3 fornece pontos finais separados para a ligação NER e entidade. As respostas para ambas as operações estão abaixo. 

#### <a name="example-ner-response"></a>Exemplo resposta NER

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


#### <a name="example-entity-linking-response"></a>Exemplo entidade que liga a resposta

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
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


## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para entidade que liga usando Text Analytics em Serviços Cognitivos. Em resumo:

* Os documentos JSON no órgão de pedido incluem um ID, texto e código linguístico.
* Os pedidos de CORREIO são enviados para um ou mais pontos finais, utilizando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
* A saída de resposta, que consiste em entidades ligadas (incluindo pontuações de confiança, compensações e links web, para cada ID do documento) pode ser utilizada em qualquer aplicação

## <a name="next-steps"></a>Próximos passos

* [Descrição Geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
