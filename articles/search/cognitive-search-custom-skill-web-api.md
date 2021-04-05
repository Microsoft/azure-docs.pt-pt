---
title: Habilidade de API web personalizada em skillsets
titleSuffix: Azure Cognitive Search
description: Alargar as capacidades das habilidades de Pesquisa Cognitiva Azure chamando para as APIs web. Utilize a habilidade de API web personalizada para integrar o seu código personalizado.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: cb5ee7d3549e433fb184b8c55c28b9a28ed89272
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011936"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Habilidade personalizada da API web em um oleoduto de enriquecimento de pesquisa cognitiva Azure

A habilidade **de API web personalizada** permite-lhe estender o enriquecimento de IA chamando para um ponto final da Web API fornecendo operações personalizadas. Semelhante às habilidades incorporadas, uma habilidade **de API web personalizada** tem entradas e saídas. Dependendo das entradas, a sua API Web recebe uma carga útil JSON quando o indexante funciona, e produz uma carga útil JSON como resposta, juntamente com um código de estado de sucesso. Espera-se que a resposta tenha as saídas especificadas pela sua habilidade personalizada. Qualquer outra resposta é considerada um erro e não são realizados enriquecimentos.

A estrutura das cargas JSON é descrita mais abaixo neste documento.

> [!NOTE]
> O indexante tentará duas vezes para determinados códigos de estado HTTP padrão devolvidos da API Web. Estes códigos de estado HTTP são: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.custom.webapiskill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `uri` | O URI da API Web para o qual será enviada a carga útil _JSON._ Apenas o esquema **https** URI é permitido |
| `httpMethod` | O método a utilizar durante o envio da carga útil. Os métodos permitidos são `PUT` ou `POST` |
| `httpHeaders` | Uma coleção de pares de valores-chave onde as teclas representam nomes e valores de cabeçalho representam valores de cabeçalho que serão enviados para a sua API Web juntamente com a carga útil. Os seguintes cabeçalhos estão proibidos de estar nesta coleção:  `Accept` , , , , , , , , `Accept-Charset` , `Accept-Encoding` `Content-Length` `Content-Type` `Cookie` `Host` `TE` `Upgrade` , `Via` |
| `timeout` | (Opcional) Quando especificado, indica o tempo limite para o cliente http que e faz a chamada da API. Deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de [duração ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Por exemplo, `PT60S` durante 60 segundos. Se não estiver definido, é escolhido um valor predefinido de 30 segundos. O tempo limite pode ser definido para um máximo de 230 segundos e um mínimo de 1 segundo. |
| `batchSize` | (Opcional) Indica quantos "registos de dados" (ver estrutura de carga útil _JSON_ abaixo) serão enviados por chamada da API. Se não for definido, é escolhido um padrão de 1000. Recomendamos que utilize este parâmetro para obter uma compensação adequada entre a produção de indexação e a carga na sua API |
| `degreeOfParallelism` | (Opcional) Quando especificado, indica o número de chamadas que o indexante fará paralelamente ao ponto final que forneceu. Pode diminuir este valor se o seu ponto final estiver a falhar abaixo de uma carga de pedido demasiado elevada, ou aumentá-lo se o seu ponto final for capaz de aceitar mais pedidos e pretender um aumento no desempenho do indexante.  Se não for definido, é utilizado um valor predefinido de 5. Pode `degreeOfParallelism` ser definido para um máximo de 10 e um mínimo de 1. |

## <a name="skill-inputs"></a>Entradas de habilidades

Não há entradas "predefinidas" para esta habilidade. Pode escolher um ou mais campos que já estariam disponíveis no momento da execução desta habilidade como entradas e a carga útil _JSON_ enviada para a API Web terá diferentes campos.

## <a name="skill-outputs"></a>Saídas de competências

Não há saídas "predefinidas" para esta habilidade. Dependendo da resposta que a sua API Web devolver, adicione campos de saída para que possam ser recolhidos a partir da resposta _JSON._


## <a name="sample-definition"></a>Definição de amostra

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Estrutura JSON de entrada de amostra

Esta estrutura _JSON_ representa a carga útil que será enviada para a sua API Web.
Seguirá sempre estes constrangimentos:

* A entidade de alto nível é chamada `values` e será uma variedade de objetos. O número de tais objetos será no máximo o `batchSize`
* Cada objeto na `values` matriz terá
    * Uma `recordId` propriedade que é uma corda **única,** usada para identificar esse registo.
    * Uma `data` propriedade que é um objeto _JSON._ Os campos da `data` propriedade corresponderão aos "nomes" especificados na `inputs` secção da definição de habilidade. O valor desses campos será a partir `source` desses campos (que podem ser de um campo no documento, ou potencialmente de outra habilidade)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Estrutura JSON de saída de amostra

A "saída" corresponde à resposta devolvida da sua API Web. A API Web só deve devolver uma carga útil _JSON_ (verificada olhando para o `Content-Type` cabeçalho de resposta) e deve satisfazer os seguintes constrangimentos:

* Deve haver uma entidade de alto nível chamada `values` que deve ser uma variedade de objetos.
* O número de objetos na matriz deve ser o mesmo que o número de objetos enviados para a API Web.
* Cada objeto deve ter:
   * Uma `recordId` propriedade
   * Uma `data` propriedade, que é um objeto onde os campos são enriquecimentos correspondentes aos "nomes" no `output` e cujo valor é considerado o enriquecimento.
   * Uma `errors` propriedade, uma matriz que enumera quaisquer erros encontrados que serão adicionados ao histórico de execução do indexante. Esta propriedade é necessária, mas pode ter um `null` valor.
   * Uma `warnings` propriedade, uma matriz que enumera quaisquer avisos encontrados que serão adicionados ao histórico de execução do indexante. Esta propriedade é necessária, mas pode ter um `null` valor.
* Os objetos na `values` matriz não precisam de estar na mesma ordem que os objetos da `values` matriz enviados como um pedido à API Web. No entanto, o `recordId` é utilizado para a correlação, pelo que qualquer registo na resposta que contenha um que não fizesse parte do pedido original à `recordId` API web será descartado.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Casos de erro
Além de a sua API Web não estar disponível, ou enviar códigos de estado não bem sucedidos, os seguintes são considerados casos erróneos:

* Se a API Web devolver um código de estado de sucesso, mas a resposta indicar que não `application/json` é, então a resposta é considerada inválida e não serão realizados enriquecimentos.
* Se houver registos **inválidos** `recordId` (sem o pedido original, ou com valores duplicados) no `values` conjunto de resposta, não será realizado qualquer enriquecimento para **esses registos.**

Para casos em que a API Web não esteja disponível ou devolva um erro HTTP, um erro amigável com quaisquer detalhes disponíveis sobre o erro HTTP será adicionado ao histórico de execução do indexante.

## <a name="see-also"></a>Ver também

+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Adicione habilidade personalizada a um oleoduto de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
+ [Exemplo: Criar uma habilidade personalizada para o enriquecimento de IA](cognitive-search-create-custom-skill-example.md)
