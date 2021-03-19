---
title: Definição de interface para habilidades personalizadas
titleSuffix: Azure Cognitive Search
description: Interface personalizada de extração de dados para habilidade personalizada web-api em um oleoduto de enriquecimento de IA em Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e78f0d1e8d6d637dfebe1ff475ab8416ba49a263
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935420"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Como adicionar uma habilidade personalizada a um oleoduto de enriquecimento de pesquisa cognitiva Azure

> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=172&end=221]

Um [oleoduto de enriquecimento](cognitive-search-concept-intro.md) em Azure Cognitive Search pode ser montado a partir [de habilidades cognitivas incorporadas,](cognitive-search-predefined-skills.md) bem como [habilidades personalizadas](cognitive-search-custom-skill-web-api.md) que você pessoalmente cria e adiciona ao pipeline. Neste artigo, aprenda a criar uma habilidade personalizada que exponha uma interface permitindo que seja incluída num pipeline de enriquecimento de IA. 

Construir uma habilidade personalizada dá-lhe uma forma de inserir transformações únicas no seu conteúdo. Uma habilidade personalizada executa de forma independente, aplicando o passo de enriquecimento que você precisa. Por exemplo, pode definir entidades personalizadas específicas de campo, construir modelos de classificação personalizadas para diferenciar contratos e documentos de negócios e financeiros, ou adicionar uma habilidade de reconhecimento de voz para se aprofundar em ficheiros áudio para conteúdo relevante. Para um exemplo passo a passo, consulte [Exemplo: Criar uma habilidade personalizada para enriquecimento de IA](cognitive-search-create-custom-skill-example.md).

 Qualquer que seja a capacidade personalizada que você precisa, existe uma interface simples e clara para ligar uma habilidade personalizada ao resto do pipeline de enriquecimento. A única exigência para a inclusão num [skillset](cognitive-search-defining-skillset.md) é a capacidade de aceitar inputs e emitir resultados de formas consumíveis dentro do skillset como um todo. O foco deste artigo está nos formatos de entrada e saída que o gasoduto de enriquecimento requer.

## <a name="web-api-custom-skill-interface"></a>Interface de habilidade personalizada da Web API

Os pontos finais de habilidades personalizados da WebAPI por tempo limite padrão se não devolverem uma resposta dentro de uma janela de 30 segundos. O gasoduto de indexação é sincronizado e a indexação produzirá um erro de tempo limite se não for recebida uma resposta nessa janela.  É possível configurar o tempo limite de tempo até 230 segundos, definindo o parâmetro de tempo limite:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Certifique-se de que o URI está seguro (HTTPS).

Atualmente, o único mecanismo para interagir com uma habilidade personalizada é através de uma interface Web API. As necessidades da API Web devem satisfazer os requisitos descritos nesta secção.

### <a name="1--web-api-input-format"></a>1. Formato de entrada de API web


> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=294&end=340]


A API Web deve aceitar uma série de registos a serem processados. Cada registo deve conter um "saco de propriedade" que é a entrada fornecida à sua API Web. 

Suponha que queira criar um simples enriquecedor que identifique a primeira data mencionada no texto de um contrato. Neste exemplo, a habilidade aceita um único contrato de *entradaTexto* como texto do contrato. A habilidade também tem uma única saída, que é a data do contrato. Para tornar o enriquecedor mais interessante, devolva este *contratoData* na forma de um tipo complexo multi-partes.

A sua API Web deve estar pronta para receber um lote de registos de entrada. Cada membro da matriz de *valores* representa a entrada para um registo específico. Cada registo é obrigado a ter os seguintes elementos:

+ Um membro *registado* que é o identificador único para um registo particular. Quando o seu enriquente retornar os resultados, deve fornecer este *registoId* para permitir que o chamador corresponda aos resultados do registo com a sua entrada.

+ Um membro *de dados,* que é essencialmente um saco de campos de entrada para cada registo.

Para ser mais concreto, pelo exemplo acima, a sua API web deve esperar pedidos que se pareçam com este aspeto:

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

### <a name="2-web-api-output-format"></a>2. Formato de saída da API web

O formato da saída é um conjunto de registos contendo um *recordId,* e um saco de propriedade 

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

Este exemplo em particular tem apenas uma saída, mas você poderia produção de mais de uma propriedade. 

### <a name="errors-and-warning"></a>Erros e Aviso

Como mostrado no exemplo anterior, pode devolver mensagens de erro e aviso para cada registo.

## <a name="consuming-custom-skills-from-skillset"></a>Consumir habilidades personalizadas a partir de skillset

Quando criar um enriquecidor de API web, pode descrever os cabeçalhos e parâmetros HTTP como parte do pedido. O snippet abaixo mostra como os parâmetros de pedido e os cabeçalhos HTTP *opcionais* podem ser descritos como parte da definição de skillset. Os cabeçalhos HTTP não são um requisito, mas permitem-lhe adicionar capacidades de configuração adicionais à sua habilidade e defini-los a partir da definição de skillset.

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

Este artigo abrangia os requisitos de interface necessários para integrar uma habilidade personalizada numa habilidade. Clique nos seguintes links para saber mais sobre habilidades personalizadas e composição skillset.

+ [Veja o nosso vídeo sobre habilidades personalizadas](https://youtu.be/fHLCE-NZeb4)
+ [Power Skills: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Exemplo: Criar uma habilidade personalizada para o enriquecimento de IA](cognitive-search-create-custom-skill-example.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)