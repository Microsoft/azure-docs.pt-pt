---
title: Habilidade personalizada da Web API em habilidades
titleSuffix: Azure Cognitive Search
description: Alargar as capacidades das habilidades de Pesquisa Cognitiva Azure, chamando para apis web. Utilize a habilidade Custom Web API para integrar o seu código personalizado.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74484125"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Habilidade personalizada da Web API em um oleoduto de enriquecimento de pesquisa cognitiva Azure

A habilidade **Custom Web API** permite-lhe estender o enriquecimento de IA, chamando para um ponto final da Web API fornecendo operações personalizadas. Semelhante às habilidades incorporadas, uma habilidade **Personalizada web API** tem inputs e saídas. Dependendo das inputs, o seu Web API recebe uma carga útil JSON quando o indexante funciona, e produz uma carga útil JSON como resposta, juntamente com um código de estado de sucesso. Espera-se que a resposta tenha as saídas especificadas pela sua habilidade personalizada. Qualquer outra resposta é considerada um erro e não são realizados enriquecimentos.

A estrutura das cargas da JSON é descrita mais abaixo neste documento.

> [!NOTE]
> O indexante irá voltar a tentar duas vezes para determinados códigos de estado padrão http devolvidos da Web API. Estes códigos de estado HTTP são: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| uri | O URI da Web API para o qual será enviada a carga útil _JSON._ Apenas o esquema **https** URI é permitido |
| httpMethod | O método a utilizar durante o envio da carga útil. Os métodos permitidos são `PUT` ou`POST` |
| httpHeaders | Uma coleção de pares de valor-chave onde as teclas representam nomes e valores de cabeçalho representam valores de cabeçalho que serão enviados para a sua Web API juntamente com a carga útil. Os seguintes cabeçalhos estão proibidos `Accept-Charset` `Accept-Encoding`de `Content-Length` `Content-Type`estar `Cookie` `Host`nesta `TE` `Upgrade`coleção: `Accept`. , , , , , ,`Via` |
| tempo limite | (Opcional) Quando especificado, indica o tempo limite para o cliente http fazer a chamada aPi. Deve ser formatado como um valor XSD "daytimeduration" (um subconjunto restrito de um valor de [duração ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Por exemplo, `PT60S` durante 60 segundos. Se não estiver definido, é escolhido um valor predefinido de 30 segundos. O tempo limite pode ser fixado para um máximo de 230 segundos e um mínimo de 1 segundo. |
| batchSize | (Opcional) Indica quantos "registos de dados" (ver estrutura de carga útil _JSON_ abaixo) serão enviados por chamada API. Se não estiver definido, é escolhido um padrão de 1000. Recomendamos que utilize este parâmetro para conseguir uma compensação adequada entre a pontuação de indexação e a carga na sua API |
| degreeOfParallelismo | (Opcional) Quando especificado, indica o número de chamadas que o indexante fará paralelamente ao ponto final que forneceu. Pode diminuir este valor se o seu ponto final estiver a falhar demasiado numa carga de pedido, ou a elevá-lo se o seu ponto final for capaz de aceitar mais pedidos e quiser um aumento no desempenho do indexante.  Se não estiver definido, é utilizado um valor predefinido de 5. O grauOfParallelismo pode ser definido para um máximo de 10 e um mínimo de 1. |

## <a name="skill-inputs"></a>Inputs de habilidade

Não há inputs "predefinidos" para esta habilidade. Você pode escolher um ou mais campos que já estariam disponíveis no momento da execução desta habilidade como inputs e a carga útil _JSON_ enviada para a Web API terá diferentes campos.

## <a name="skill-outputs"></a>Saídas de habilidades

Não há saídas "predefinidas" para esta habilidade. Dependendo da resposta que a Sua API web devolverá, adicione campos de saída para que possam ser recolhidos a partir da resposta _JSON._


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

Esta estrutura _JSON_ representa a carga útil que será enviada para a sua Web API.
Seguirá sempre estes constrangimentos:

* A entidade de alto `values` nível é chamada e será uma variedade de objetos. O número de tais objetos será no máximo o`batchSize`
* Cada objeto `values` na matriz terá
    * Uma `recordId` propriedade que é uma corda **única,** usada para identificar esse registo.
    * Uma `data` propriedade que é um objeto _JSON._ Os campos `data` da propriedade corresponderão aos "nomes" especificados na `inputs` secção da definição de competências. O valor desses campos será `source` do dos campos (que podem ser de um campo no documento, ou potencialmente de outra habilidade)

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

A "saída" corresponde à resposta devolvida da sua API Web. A Web API só deve devolver uma carga útil _JSON_ (verificada olhando para o `Content-Type` cabeçalho de resposta) e deve satisfazer os seguintes constrangimentos:

* Deve haver uma entidade de `values` alto nível chamada que deve ser uma variedade de objetos.
* O número de objetos na matriz deve ser o mesmo que o número de objetos enviados para a Web API.
* Cada objeto deve ter:
   * Uma `recordId` propriedade
   * Uma `data` propriedade, que é um objeto onde os campos são `output` enriquecimentos que combinam com os "nomes" no e cujo valor é considerado o enriquecimento.
   * Uma `errors` propriedade, um conjunto que enumera todos os erros encontrados que serão adicionados ao histórico de execução do indexador. Esta propriedade é necessária, mas `null` pode ter um valor.
   * Uma `warnings` propriedade, um conjunto listando quaisquer avisos encontrados que serão adicionados ao histórico de execução indexante. Esta propriedade é necessária, mas `null` pode ter um valor.
* Os objetos `values` da matriz não precisam de estar `values` na mesma ordem que os objetos da matriz enviados como um pedido à Web API. No entanto, o é `recordId` utilizado para a `recordId` correlação, pelo que qualquer registo na resposta que contenha um que não fizesse parte do pedido original à API web será descartado.

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
Além da sua API Web não estar disponível, ou enviar códigos de estado não-bem-sucedidos, os seguintes casos são considerados casos errados:

* Se a Web API devolver um código de estado `application/json` de sucesso, mas a resposta indica que não é então a resposta é considerada inválida e não serão realizados enriquecimentos.
* Se houver registos **inválidos** (sem `recordId` o pedido original, ou `values` com valores duplicados) no conjunto de resposta, não será realizado qualquer enriquecimento para **esses** registos.

Para casos em que a Web API está indisponível ou devolve um erro HTTP, um erro amigável com quaisquer detalhes disponíveis sobre o erro HTTP será adicionado ao histórico de execução do indexador.

## <a name="see-also"></a>Consulte também

+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Adicione habilidade personalizada a um oleoduto de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
+ [Exemplo: Criar uma habilidade personalizada para enriquecimento de IA](cognitive-search-create-custom-skill-example.md)
