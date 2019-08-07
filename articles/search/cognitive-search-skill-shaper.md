---
title: Habilidade de pesquisa cognitivar de Modelador-Azure Search
description: Extraia metadados e informações estruturadas de dados não estruturados e formate-os como um tipo complexo em um pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 16bb7d84bbf19081c146aaac13ecc798610bc4bc
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840963"
---
#   <a name="shaper-cognitive-skill"></a>Habilidade cognitiva do modelador

A habilidade de Modelador consolida várias entradas em um [tipo complexo](search-howto-complex-data-types.md) que pode ser referenciado posteriormente no pipeline de enriquecimento. A habilidade de Modelador permite criar essencialmente uma estrutura, definir o nome dos membros dessa estrutura e atribuir valores a cada membro. Exemplos de campos consolidados úteis em cenários de pesquisa incluem a combinação de um nome e sobrenome em uma única estrutura, cidade e estado em uma única estrutura, ou nome e DataDeNascimento em uma única estrutura para estabelecer identidade exclusiva.

Além disso, a habilidade de Modelador ilustrada no [cenário 3](#nested-complex-types) adiciona uma propriedade *sourceContext* opcional à entrada. As propriedades *Source* e *sourceContext* são mutuamente exclusivas. Se a entrada estiver no contexto da habilidade, simplesmente use *Source*. Se a entrada estiver em um contexto *diferente* do contexto de habilidade, use o *sourceContext*. O *sourceContext* exige que você defina uma entrada aninhada com o elemento específico que está sendo endereçado como a origem. 

O nome de saída é sempre "output". Internamente, o pipeline pode mapear um nome diferente, como "analyzedText", conforme mostrado nos exemplos abaixo, mas a própria habilidade de Modelador retorna "output" na resposta. Isso pode ser importante se você estiver Depurando documentos aprimorados e perceber a discrepância de nomenclatura, ou se você criar uma habilidade personalizada e estiver estruturando a resposta por conta própria.

> [!NOTE]
> A habilidade do modelador não está associada a uma API de serviços cognitivas e você não é cobrado por usá-la. No entanto, você ainda deve [anexar um recurso de serviços cognitivas](cognitive-search-attach-cognitive-services.md)para substituir a opção de recurso **gratuito** que limita você a um pequeno número de aprimoramentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Cenário 1: tipos complexos

Considere um cenário em que você deseja criar uma estrutura chamada *analyzedText* que tem dois membros: *texto* e *sentimentos*, respectivamente. Em um índice de Azure Search, um campo pesquisável de várias partes é chamado de *tipo complexo* e geralmente é criado quando os dados de origem têm uma estrutura complexa correspondente que mapeia para ele.

No entanto, outra abordagem para a criação de tipos complexos é por meio da habilidade do modelador. Ao incluir essa habilidade em um configurador, as operações na memória durante o processamento do Configurador de habilidades podem gerar formas de dados com estruturas aninhadas, que podem então ser mapeadas para um tipo complexo no índice. 

A definição de habilidade de exemplo a seguir fornece os nomes de membro como a entrada. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Índice de exemplo

Um configurador de qualificações é invocado por um indexador e um indexador requer um índice. Uma representação de campo complexo no índice pode ser semelhante ao exemplo a seguir. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Entrada de habilidade

Um documento JSON de entrada que fornece entrada utilizável para essa habilidade de **forma** pode ser:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Saída de habilidades

A habilidade de Modelador gera um novo elemento chamado *analyzedText* com os elementos combinados de *texto* e de *sentimentos*. Essa saída está em conformidade com o esquema de índice. Ele será importado e indexado em um índice de Azure Search.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Cenário 2: consolidação de entrada

Em outro exemplo, imagine que em diferentes estágios de processamento de pipeline, você extraiu o título de um livro e títulos de capítulo em diferentes páginas do livro. Agora você pode criar uma única estrutura composta por essas várias entradas.

A definição de habilidade do modelador para esse cenário pode ser semelhante ao exemplo a seguir:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Saída de habilidades
Nesse caso, o **Shaper** mescla todos os títulos de capítulo para criar uma única matriz. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Cenário 3: consolidação de entrada de contextos aninhados

Imagine que você tenha o título, os capítulos e o conteúdo de um livro e tenha executado o reconhecimento de entidade e frases-chave sobre o conteúdo e agora precisa agregar resultados das diferentes habilidades em uma única forma com o nome do capítulo, entidades e frases-chave.

A definição de habilidade do modelador para esse cenário pode ser semelhante ao exemplo a seguir:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Saída de habilidades
Nesse caso, o modelador cria um tipo complexo. Essa estrutura existe na memória. Se você quiser salvá-lo em uma [loja de conhecimento](knowledge-store-concept-intro.md), deverá criar uma projeção em seu Skills que define as características de armazenamento.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Como usar tipos complexos](search-howto-complex-data-types.md)
+ [Visão geral da loja de conhecimento](knowledge-store-concept-intro.md)
+ [Como começar a usar a loja de conhecimento](knowledge-store-howto.md)