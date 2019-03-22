---
title: Definição de interface para competências personalizadas na pesquisa cognitiva - Azure Search
description: Interface de extração de dados personalizados para habilidade personalizado da web api no pipeline de pesquisa cognitiva no Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 2c43a6b64f7b7f4f1adae78dca77fe71cf538e5e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870862"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Como adicionar uma habilidade personalizada para um pipeline de pesquisa cognitiva

Uma [pipeline de indexação de pesquisa cognitiva](cognitive-search-concept-intro.md) no Azure Search pode ser montada a partir de [predefinidos habilidades](cognitive-search-predefined-skills.md) , bem como [competências personalizadas](cognitive-search-custom-skill-web-api.md) que criar e adicionar a pessoalmente o pipeline. Neste artigo, saiba como criar uma habilidade personalizada que expõe uma interface que permite para ser incluída num pipeline de pesquisa cognitiva. 

Criar uma habilidade personalizada dá-lhe uma forma de inserir transformações exclusivas ao seu conteúdo. Uma habilidade personalizada é executado de forma independente, aplicar qualquer passo de enriquecimento necessitar. Por exemplo, poderia definir entidades personalizadas de campo específico, criar modelos de classificação personalizada para diferenciar empresariais e contratos financeiros e documentos ou adicionar uma habilidade de reconhecimento de voz para alcançar-se em arquivos de áudio para conteúdo relevante. Para obter um exemplo passo a passo, consulte [exemplo: criar uma habilidade personalizada](cognitive-search-create-custom-skill-example.md).

 Seja qual for necessitar, existe uma interface simples e clara para ligar uma habilidade personalizada para o resto do pipeline enriquecimento de capacidade personalizado. O único requisito para inclusão numa [conjunto de capacidades](cognitive-search-defining-skillset.md) é a capacidade de aceitar entradas e emitir as saídas de formas consumidas no conjunto de capacidades como um todo. O foco deste artigo é sobre os formatos de entrada e saídos que requer que o pipeline de melhoria.

## <a name="web-api-custom-skill-interface"></a>Interface de habilidades personalizado da API Web

WebAPI habilidade os pontos finais personalizados por tempo limite predefinido, se eles não devolverem uma resposta dentro de uma janela de 30 segundos. O pipeline de indexação é síncrono e indexação produzirá um erro de tempo limite se não for recebida uma resposta nessa janela.  É possível configurar o tempo limite para ser até 90 segundos, definindo o parâmetro de tempo limite:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 90 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT90S",
```

Atualmente, o mecanismo de apenas para interagir com uma habilidade personalizada é através de uma interface de Web API. A API Web precisa têm de cumprir os requisitos descritos nesta secção.

### <a name="1--web-api-input-format"></a>1.  Formato de entrada de API Web

A API Web tem de aceitar uma matriz de registos a serem processados. Cada registo tem de conter um "recipiente" Isto é a entrada fornecida para a sua API Web. 

Suponha que deseja criar um enricher simple que identifica a primeira data mencionada no texto de um contrato. Neste exemplo, a habilidade aceita uma entrada única *contractText* como o texto do contrato. A habilidade também tem uma única saída, o que é a data do contrato. Para tornar o enricher mais interessantes, devolver *contractDate* na forma de um tipo complexo com várias parte.

A API Web deve estar pronta para receber um lote de registos de entrada. Cada membro de *valores* matriz representa a entrada de um registo específico. Cada registro é necessário para ter os seguintes elementos:

+ R *recordId* membro que é o identificador exclusivo de um determinado registo. Quando seu enricher retorna os resultados, deve fornecer isso *recordId* para permitir que o autor da chamada de acordo com os resultados de registo para o seu parecer.

+ R *dados* membro, o que é essencialmente um conjunto de campos de entrada para cada registo.

Para ser mais concreta, por exemplo acima, a API Web deve esperar que os pedidos que ter o seguinte aspeto:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
Na realidade, o seu serviço pode obter chamado com centenas ou milhares de registos em vez de apenas três mostrado aqui.

### <a name="2-web-api-output-format"></a>2. Formato de saída da API Web

O formato de saída é um conjunto de registos que contém um *recordId*e uma matriz de propriedades 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Esse exemplo específico tem apenas uma saída, mas poderia transmitir mais de uma propriedade. 

### <a name="errors-and-warning"></a>Erros e de aviso

Conforme mostrado no exemplo anterior, podem devolver erros e mensagens de aviso para cada registo.

## <a name="consuming-custom-skills-from-skillset"></a>Consumir competências personalizadas do conjunto de capacidades

Quando cria um enricher de Web API, pode descrever os cabeçalhos HTTP e os parâmetros como parte do pedido. O fragmento abaixo mostra como parâmetros do pedido e cabeçalhos HTTP podem ser descritos como parte da definição do conjunto de capacidades.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes

+ [Exemplo: Criar uma habilidade personalizada para a API de texto a traduzir](cognitive-search-create-custom-skill-example.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar conjunto de capacidades (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos plena](cognitive-search-output-field-mapping.md)
