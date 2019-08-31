---
title: Definição de interface para habilidades personalizadas na pesquisa cognitiva-Azure Search
description: Interface de extração de dados personalizada para habilidade personalizada da API Web no pipeline de pesquisa cognitiva no Azure Search.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: c424fec550a52830db8e32682b68f49f486ba119
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183488"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Como adicionar uma habilidade personalizada a um pipeline de pesquisa cognitiva

Um [pipeline de indexação de pesquisa cognitiva](cognitive-search-concept-intro.md) no Azure Search pode ser montado de [habilidades](cognitive-search-predefined-skills.md) predefinidas, bem como [habilidades personalizadas](cognitive-search-custom-skill-web-api.md) que você cria pessoalmente e adiciona ao pipeline. Neste artigo, saiba como criar uma habilidade personalizada que expõe uma interface que permite que ela seja incluída em um pipeline de pesquisa cognitiva. 

A criação de uma habilidade personalizada oferece uma maneira de inserir transformações exclusivas para seu conteúdo. Uma habilidade personalizada é executada de forma independente, aplicando qualquer etapa de enriquecimento necessária. Por exemplo, você pode definir entidades personalizadas específicas de campo, criar modelos de classificação personalizados para diferenciar contratos comerciais e financeiros e documentos ou adicionar uma habilidade de reconhecimento de fala para alcançar mais detalhes em arquivos de áudio para conteúdo relevante. Para obter um exemplo passo a passo, consulte [exemplo: Criar uma habilidade personalizada para pesquisa](cognitive-search-create-custom-skill-example.md)cognitiva.

 Seja qual for a funcionalidade personalizada que você precisa, há uma interface simples e clara para conectar uma habilidade personalizada ao restante do pipeline de enriquecimento. O único requisito para inclusão em um configurador de [habilidades](cognitive-search-defining-skillset.md) é a capacidade de aceitar entradas e emitir saídas de maneiras consumíveis dentro do consumível como um todo. O foco deste artigo é nos formatos de entrada e saída que o pipeline de enriquecimento exige.

## <a name="web-api-custom-skill-interface"></a>Interface de habilidade personalizada da API Web

Pontos de extremidade de habilidade de WebAPI personalizados por tempo limite padrão se eles não retornam uma resposta em uma janela de 30 segundos. O pipeline de indexação será síncrono e a indexação produzirá um erro de tempo limite se uma resposta não for recebida nessa janela.  É possível configurar o tempo limite para até 230 segundos, definindo o parâmetro timeout:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Atualmente, o único mecanismo para interagir com uma habilidade personalizada é por meio de uma interface de API da Web. As necessidades da API Web devem atender aos requisitos descritos nesta seção.

### <a name="1--web-api-input-format"></a>1.  Formato de entrada da API Web

A API da Web deve aceitar uma matriz de registros a serem processados. Cada registro deve conter um "recipiente de propriedades" que é a entrada fornecida para sua API da Web. 

Suponha que você queira criar um enriquecimento simples que identifique a primeira data mencionada no texto de um contrato. Neste exemplo, a habilidade aceita uma única entrada *contractText* como o texto do contrato. A habilidade também tem uma única saída, que é a data do contrato. Para tornar o mais sofisticado mais interessante, retorne esse *contractDate* na forma de um tipo complexo de várias partes.

Sua API Web deve estar pronta para receber um lote de registros de entrada. Cada membro da matriz de *valores* representa a entrada para um registro específico. Cada registro é necessário para ter os seguintes elementos:

+ Um membro recordId que é o identificador exclusivo de um registro específico. Quando o enriquecimento retorna os resultados, ele deve fornecer esse recordId para permitir que o chamador corresponda os resultados do registro à sua entrada.

+ Um membro de *dados* , que é essencialmente um conjunto de campos de entrada para cada registro.

Para ser mais concreto, de acordo com o exemplo acima, sua API Web deve esperar solicitações parecidas com esta:

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
Na realidade, seu serviço pode ser chamado com centenas ou milhares de registros, em vez de apenas os três mostrados aqui.

### <a name="2-web-api-output-format"></a>2. Formato de saída da API Web

O formato da saída é um conjunto de registros contendo um *recordId*e um recipiente de propriedades 

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

Esse exemplo específico tem apenas uma saída, mas você pode gerar mais de uma propriedade. 

### <a name="errors-and-warning"></a>Erros e aviso

Conforme mostrado no exemplo anterior, você pode retornar mensagens de erro e de aviso para cada registro.

## <a name="consuming-custom-skills-from-skillset"></a>Consumindo habilidades personalizadas do qualificable

Quando você cria um aprimorador de API Web, pode descrever os cabeçalhos e os parâmetros HTTP como parte da solicitação. O trecho de código a seguir mostra como os parâmetros de solicitação e os cabeçalhos HTTP podem ser descritos como parte da definição do congrau de habilidade.

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

+ [Exemplo: Criando uma habilidade personalizada para pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Criar conconhecimento (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos aprimorados](cognitive-search-output-field-mapping.md)
