---
title: Definição de interface para habilidades personalizadas
titleSuffix: Azure Cognitive Search
description: Interface personalizada de extração de dados para habilidade personalizada web-api em um pipeline de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 78f5f6eda28bed164668445b5671dad92f8dedd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500258"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Como adicionar uma habilidade personalizada a um oleoduto de enriquecimento de pesquisa cognitiva Azure

Um [oleoduto](cognitive-search-concept-intro.md) de enriquecimento em Azure Cognitive Search pode ser montado a partir de [habilidades cognitivas incorporadas,](cognitive-search-predefined-skills.md) bem como [habilidades personalizadas](cognitive-search-custom-skill-web-api.md) que você pessoalmente cria e adiciona ao pipeline. Neste artigo, aprenda a criar uma habilidade personalizada que exponha uma interface permitindo que seja incluída num pipeline de enriquecimento de IA. 

Construir uma habilidade personalizada dá-lhe uma forma de inserir transformações únicas no seu conteúdo. Uma habilidade personalizada executa de forma independente, aplicando o passo de enriquecimento que você precisa. Por exemplo, pode definir entidades personalizadas específicas do campo, construir modelos de classificação personalizada para diferenciar contratos e documentos comerciais e financeiros, ou adicionar uma habilidade de reconhecimento de voz para aprofundar os ficheiros áudio para conteúdos relevantes. Para um exemplo passo a passo, consulte [Exemplo: Criar uma habilidade personalizada para](cognitive-search-create-custom-skill-example.md)o enriquecimento de IA .

 Qualquer que seja a capacidade personalizada que você precisa, há uma interface simples e clara para conectar uma habilidade personalizada ao resto do pipeline de enriquecimento. O único requisito para a inclusão num [skillset](cognitive-search-defining-skillset.md) é a capacidade de aceitar entradas e emitir saídas de formas consumíveis dentro do skillset como um todo. O foco deste artigo está nos formatos de entrada e saída que o gasoduto de enriquecimento necessita.

## <a name="web-api-custom-skill-interface"></a>Interface de habilidade personalizada Da API web

Pontos finais de habilidade WebAPI personalizados por tempo de predefinição se não devolverem uma resposta dentro de uma janela de 30 segundos. O gasoduto de indexação é sincronizado e a indexação produzirá um erro de tempo se não for recebida uma resposta nessa janela.  É possível configurar o tempo de paragem até 230 segundos, definindo o parâmetro de tempo:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Certifique-se de que o URI está seguro (HTTPS).

Atualmente, o único mecanismo para interagir com uma habilidade personalizada é através de uma interface Web API. As necessidades da Web API devem satisfazer os requisitos descritos nesta secção.

### <a name="1--web-api-input-format"></a>1. Formato de entrada web API

A Web API deve aceitar uma série de registos a serem processados. Cada registo deve conter um "saco de propriedade" que é a entrada fornecida à sua Web API. 

Suponha que queira criar um simples enriquecedor que identifique a primeira data mencionada no texto de um contrato. Neste exemplo, a habilidade aceita um único contrato de *entradaText* como texto do contrato. A habilidade também tem uma única saída, que é a data do contrato. Para tornar o enriquecedor mais interessante, devolva este *contratoData* na forma de um tipo complexo multi-partes.

A Sua Web API deve estar pronta para receber um lote de registos de entrada. Cada membro da matriz de *valores* representa a entrada para um determinado registo. Cada registo é necessário para ter os seguintes elementos:

+ Um membro *do recordId* que é o identificador único para um determinado registo. Quando o seu enriquecedor devolve os resultados, deve fornecer este *id de gravação* de modo a permitir que o chamador corresponda aos resultados do registo com a sua entrada.

+ Um membro *de dados,* que é essencialmente um saco de campos de entrada para cada registo.

Para ser mais concreto, segundo o exemplo acima, a sua API Web deve esperar pedidos que se pareçam com este:

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
Na realidade, o seu serviço pode ser chamado com centenas ou milhares de registos em vez de apenas os três mostrados aqui.

### <a name="2-web-api-output-format"></a>2. Formato de saída web API

O formato da saída é um conjunto de registos que contêm um *recordId,* e um saco de propriedade 

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

Este exemplo em particular tem apenas uma saída, mas você poderia obter mais do que uma propriedade. 

### <a name="errors-and-warning"></a>Erros e Aviso

Como mostrado no exemplo anterior, pode devolver erros e mensagens de aviso para cada registo.

## <a name="consuming-custom-skills-from-skillset"></a>Consumir habilidades personalizadas a partir de skillset

Ao criar um enriquecedor de API web, pode descrever os cabeçalhos e parâmetros HTTP como parte do pedido. O corte abaixo mostra como os parâmetros de pedido e os cabeçalhos *OPcionais* HTTP podem ser descritos como parte da definição de skillset. Os cabeçalhos HTTP não são um requisito, mas permitem-lhe adicionar capacidades de configuração adicionais à sua habilidade e defini-las a partir da definição de skillset.

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

## <a name="next-steps"></a>Passos seguintes

Este artigo cobria os requisitos de interface necessários para integrar uma habilidade personalizada num skillset. Clique nos seguintes links para saber mais sobre habilidades personalizadas e composição de habilidades.

+ [Power Skills: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Exemplo: Criar uma habilidade personalizada para enriquecimento de IA](cognitive-search-create-custom-skill-example.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
