---
title: Habilidade de pesquisa cognitiva personalizado - Azure Search
description: Estender recursos dos conjuntos de habilidades de pesquisa cognitiva ao chamar as APIs da Web
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: e5f7ee172563a81d45e3a35da2cfc7e8731de48d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023862"
---
# <a name="custom-web-api-skill"></a>Habilidade de Web API personalizada

O **API Web personalizada** habilidade permite-lhe expandir a pesquisa cognitiva ao chamar um ponto final de Web API fornecer operações personalizadas. Semelhante à habilidades internas, uma **a API Web personalizada** habilidade tem entradas e saídas. Consoante as entradas, a API Web recebe um payload JSON ao executar o indexador e produz um payload JSON como uma resposta, juntamente com um código de estado de êxito. A resposta é esperada que tem as saídas especificadas pelo seu habilidades personalizada. Qualquer outro tipo de resposta é considerado um erro e não possível é executadas.

A estrutura das cargas JSON são descritas, ainda mais para baixo neste documento.

> [!NOTE]
> O indexador irá repetir duas vezes para determinados códigos de estado HTTP padrão retornados da Web API. Esses códigos de estado HTTP são: 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parâmetros de habilidades

Parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| uri | O URI da API Web ao qual o _JSON_ payload será enviado. Apenas **https** é permitido o esquema de URI |
| httpMethod | O método a utilizar ao enviar o payload. Permitidos são métodos `PUT` ou `POST` |
| httpHeaders | Uma coleção de pares chave-valor, onde as chaves representam nomes de cabeçalho e valores representam os valores de cabeçalho que serão enviados para a sua API Web, juntamente com a carga. Os seguintes cabeçalhos são proibidos de que está a ser nesta coleção: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| tempo limite | (Opcional) Quando especificado, indica o tempo limite para o cliente de http que efetua a chamada de API. Tem de ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um [duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) valor). Por exemplo, `PT60S` durante 60 segundos. Se não for um valor predefinido de 30 segundos do conjunto, é escolhido. O tempo limite pode ser definido para um máximo de 90 segundos e um mínimo de 1 segundo. |
| batchSize | (Opcional) Indica o número "registos de dados" (consulte _JSON_ estrutura de payload abaixo) serão enviadas por chamada de API. Se não for uma predefinição de 1000 do conjunto, é escolhida. Recomendamos que efetue a utilização deste parâmetro para alcançar uma compensação adequada entre o débito de indexação e de carga na sua API |

## <a name="skill-inputs"></a>Entradas de habilidades

Não existem sem entradas "predefinidas" para essa habilidade. Pode escolher um ou mais campos que consumiriam já disponíveis no momento da execução dessa habilidade como entradas e o _JSON_ payload enviado para a API da Web terá campos diferentes.

## <a name="skill-outputs"></a>Saídas de habilidades

Não existem sem saídas "predefinidas" para essa habilidade. Consoante a resposta irá devolver a API Web, adicione campos de saída para que eles podem ser detetados a partir da _JSON_ resposta.


## <a name="sample-definition"></a>Definição de exemplo

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
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
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Exemplo de estrutura de entrada de JSON

Isso _JSON_ estrutura representa o payload que será enviado para a API Web.
Sempre seguirá essas restrições:

* A entidade de nível superior é chamada `values` e será uma matriz de objetos. O número desses objetos será no máximo o `batchSize`
* Cada objeto no `values` matriz terá
    * R `recordId` propriedade que é um **exclusivo** cadeia de caracteres, utilizada para identificar esse registo.
    * R `data` propriedade que é um _JSON_ objeto. Os campos do `data` a propriedade será corresponde a "nomes" especificados no `inputs` secção da definição de habilidades. O valor desses campos serão do `source` desses campos (que poderia ser de um campo no documento ou, potencialmente, de outra habilidade)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Estrutura JSON de saída de exemplo

Corresponde a "saída" para a resposta devolvida pelo sua API Web. A API Web deverá devolver apenas um _JSON_ payload (verificado ao observar o `Content-Type` cabeçalho de resposta) e deve satisfazer as seguintes restrições:

* Deve haver uma entidade de nível superior denominada `values` que deve ser uma matriz de objetos.
* O número de objetos na matriz deve ser igual ao número de objetos enviados para a API Web.
* Cada objeto deve ter:
   * A `recordId` propriedade
   * Uma `data` propriedade, que é um objeto onde os campos são possível correspondência "nomes" no `output` e cujo valor é considerado a melhoria do utilizador.
   * Um `errors` propriedade, uma matriz de listagem quaisquer erros encontrados que será adicionada ao histórico de execução do indexador. Esta propriedade é necessária, mas pode ter um `null` valor.
   * A `warnings` propriedade, uma matriz listagem quaisquer avisos encontrados que serão adicionados ao histórico de execução do indexador. Esta propriedade é necessária, mas pode ter um `null` valor.
* Os objetos na `values` matriz não tem de ser na mesma ordem como os objetos no `values` matriz enviado como um pedido para a API Web. No entanto, o `recordId` é utilizado para correlação, para que qualquer registo na resposta que contém um `recordId` que não fazia parte do pedido original para a API Web será rejeitada.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>Casos de erro
Além de sua API Web a ser indisponível ou envio os códigos de estado não concluída com êxito a seguir é considerada casos errados:

* Se a API Web devolve um código de estado de êxito, mas a resposta indica que não é `application/json` , em seguida, a resposta é considerada inválida e não possível será executada.
* Se existirem **inválido** (com `recordId` não na solicitação original ou com valores duplicados) registos na resposta `values` matriz, nenhuma melhoria será efetuada para **esses** registos.

Para casos quando a API Web não está disponível ou retorna um erro HTTP, um erro amigável com todos os detalhes disponíveis sobre o erro HTTP será adicionado ao histórico de execução do indexador.

## <a name="see-also"></a>Consulte também

+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Adicionar a habilidade personalizada a pesquisa cognitiva](cognitive-search-custom-skill-interface.md)
+ [Criar uma habilidade personalizada com a API de tradução de texto](cognitive-search-create-custom-skill-example.md)