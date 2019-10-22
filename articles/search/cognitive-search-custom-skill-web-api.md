---
title: Habilidades de pesquisa cognitiva personalizada-Azure Search
description: Estenda os recursos de habilidades de pesquisa cognitiva chamando para APIs da Web
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: a148f974671e0d909591cbf24a433384a7570842
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693297"
---
# <a name="custom-web-api-skill"></a>Habilidades da API Web personalizada

A habilidade **personalizada da API Web** permite que você estenda a pesquisa cognitiva chamando para um ponto de extremidade da API Web fornecendo operações personalizadas. Semelhante às habilidades internas, uma habilidade de **API Web personalizada** tem entradas e saídas. Dependendo das entradas, sua API da Web recebe uma carga JSON quando o indexador é executado e gera uma carga JSON como uma resposta, juntamente com um código de status de êxito. Espera-se que a resposta tenha as saídas especificadas por sua habilidade personalizada. Qualquer outra resposta é considerada um erro e nenhum aperfeiçoamento é realizado.

A estrutura dos conteúdos JSON é descrita mais adiante neste documento.

> [!NOTE]
> O indexador tentará novamente duas vezes para determinados códigos de status HTTP padrão retornados da API Web. Esses códigos de status HTTP são: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Custom. WebApiSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| URI | O URI da API Web para a qual o conteúdo _JSON_ será enviado. Somente o esquema de URI **https** é permitido |
| httpMethod | O método a ser usado ao enviar a carga. Os métodos permitidos são `PUT` ou `POST` |
| httpHeaders | Uma coleção de pares chave-valor em que as chaves representam nomes de cabeçalho e valores representam valores de cabeçalho que serão enviados à sua API Web junto com a carga. Os cabeçalhos a seguir estão proibidos de estarem nesta coleção: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| tempo limite | Adicional Quando especificado, indica o tempo limite para o cliente http fazer a chamada à API. Ele deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de [duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Por exemplo, `PT60S` por 60 segundos. Se não estiver definido, um valor padrão de 30 segundos será escolhido. O tempo limite pode ser definido como um máximo de 230 segundos e um mínimo de 1 segundo. |
| batchSize | Adicional Indica quantos "registros de dados" (consulte a estrutura de carga _JSON_ abaixo) será enviado por chamada à API. Se não estiver definido, um padrão de 1000 será escolhido. Recomendamos que você use esse parâmetro para obter uma compensação adequada entre a indexação da taxa de transferência e a carga em sua API |

## <a name="skill-inputs"></a>Entradas de habilidades

Não há nenhuma entrada "predefinida" para essa habilidade. Você pode escolher um ou mais campos que já estarão disponíveis no momento da execução dessa habilidade como entradas e o conteúdo _JSON_ enviado para a API da Web terá campos diferentes.

## <a name="skill-outputs"></a>Saídas de habilidades

Não há nenhuma saída "predefinida" para essa habilidade. Dependendo da resposta que sua API Web retornará, adicione os campos de saída para que eles possam ser selecionados da resposta _JSON_ .


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
## <a name="sample-input-json-structure"></a>Estrutura JSON de entrada de exemplo

Essa estrutura _JSON_ representa o conteúdo que será enviado à sua API Web.
Ele sempre seguirá estas restrições:

* A entidade de nível superior é chamada de `values` e será uma matriz de objetos. O número desses objetos será no máximo o `batchSize`
* Cada objeto na matriz de `values` terá
    * Uma propriedade `recordId` que é uma cadeia de caracteres **exclusiva** , usada para identificar esse registro.
    * Uma propriedade `data` que é um objeto _JSON_ . Os campos da propriedade `data` corresponderão aos "nomes" especificados na seção `inputs` da definição de habilidade. O valor desses campos será da `source` desses campos (que pode ser de um campo no documento ou potencialmente de outra habilidade)

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

A "saída" corresponde à resposta retornada de sua API da Web. A API Web deve retornar apenas uma carga _JSON_ (verificada examinando o cabeçalho de resposta `Content-Type`) e deve atender às seguintes restrições:

* Deve haver uma entidade de nível superior chamada `values` que deve ser uma matriz de objetos.
* O número de objetos na matriz deve ser igual ao número de objetos enviados para a API da Web.
* Cada objeto deve ter:
   * Uma propriedade `recordId`
   * Uma propriedade `data`, que é um objeto em que os campos são aprimoramentos que correspondem aos "nomes" na `output` e cujo valor é considerado o enriquecimento.
   * Uma propriedade `errors`, uma matriz listando os erros encontrados que serão adicionados ao histórico de execução do indexador. Essa propriedade é necessária, mas pode ter um valor `null`.
   * Uma propriedade `warnings`, uma matriz listando quaisquer avisos encontrados que serão adicionados ao histórico de execução do indexador. Essa propriedade é necessária, mas pode ter um valor `null`.
* Os objetos na matriz de `values` não precisam estar na mesma ordem que os objetos na matriz de `values` enviados como uma solicitação para a API da Web. No entanto, o `recordId` é usado para correlação, de modo que qualquer registro na resposta que contenha um `recordId` que não faça parte da solicitação original para a API da Web será Descartado.

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
Além de sua API Web estar indisponível ou enviar códigos de status não bem-sucedidos, os seguintes são considerados casos errados:

* Se a API da Web retornar um código de status de êxito, mas a resposta indicar que não é `application/json`, a resposta será considerada inválida e nenhum aprimoramento será executado.
* Se houver um valor **inválido** (com `recordId` não nos registros solicitação original ou com valores duplicados) na `values` matriz de resposta, nenhum enriquecimento será executado para **esses** registros.

Para casos em que a API Web não está disponível ou retorna um erro de HTTP, um erro amigável com todos os detalhes disponíveis sobre o erro HTTP será adicionado ao histórico de execução do indexador.

## <a name="see-also"></a>Ver também

+ [Habilidades de energia: um repositório de habilidades personalizadas](https://aka.ms/powerskills)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Adicionar habilidades personalizadas à pesquisa cognitiva](cognitive-search-custom-skill-interface.md)
+ [Exemplo: criando uma habilidade personalizada para pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)