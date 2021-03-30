---
title: Competência cognitiva do formulador
titleSuffix: Azure Cognitive Search
description: Extrair metadados e informação estruturada a partir de dados não estruturados e moldá-lo como um tipo complexo num oleoduto de enriquecimento de IA em Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 81eb0e60befc544a6c3bee8f04e901b6a5e472bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85560815"
---
# <a name="shaper-cognitive-skill"></a>Competência cognitiva do formulador

A habilidade **Shaper** consolida várias entradas num [tipo complexo](search-howto-complex-data-types.md) que pode ser referenciado mais tarde no gasoduto de enriquecimento. A habilidade **do Shaper** permite-lhe essencialmente criar uma estrutura, definir o nome dos membros dessa estrutura e atribuir valores a cada membro. Exemplos de campos consolidados úteis em cenários de pesquisa incluem combinar um primeiro e último nome em uma única estrutura, cidade e estado em uma única estrutura, ou nome e data de nascimento em uma única estrutura para estabelecer identidade única.

Adicionalmente, a habilidade **Shaper** ilustrada no [cenário 3](#nested-complex-types) adiciona uma propriedade *opcional OrigemContext* à entrada. As propriedades *source* e *SourceContext* são mutuamente exclusivas. Se a entrada estiver no contexto da habilidade, basta utilizar *a fonte*. Se a entrada estiver num contexto *diferente* do contexto de habilidade, utilize o *texto da fonte*. A *fonteContexto* requer que você defina uma entrada aninhada com o elemento específico sendo endereçado como a fonte. 

O nome de saída é sempre "output". Internamente, o pipeline pode mapear um nome diferente, como "analisoutex" como mostrado nos exemplos abaixo, mas a própria habilidade **shaper** devolve "output" na resposta. Isto pode ser importante se estiver a depurar documentos enriquecidos e notar a discrepância de nomeação, ou se você constrói uma habilidade personalizada e está estruturando a resposta por si mesmo.

> [!NOTE]
> A habilidade **shaper** não está ligada a uma API de Serviços Cognitivos e você não é cobrado por usá-lo. No entanto, deve ainda [anexar um recurso de Serviços Cognitivos](cognitive-search-attach-cognitive-services.md)para anular a opção de recursos **Gratuitos** que o limita a um pequeno número de enriquecimentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Cenário 1: tipos complexos

Considere um cenário em que pretende criar uma estrutura chamada *Textt analisado* que tenha dois membros: *texto* e *sentimento,* respectivamente. Num índice, um campo pesmável em várias partes é chamado de *tipo complexo* e é muitas vezes criado quando os dados de origem têm uma estrutura complexa correspondente que mapeia para ele.

No entanto, outra abordagem para criar tipos complexos é através da habilidade **Shaper.** Ao incluir esta habilidade num skillset, as operações na memória durante o processamento de skillset podem dar formas de dados de produção com estruturas aninhadas, que podem ser mapeadas para um tipo complexo no seu índice. 

A definição de habilidade de exemplo a seguir fornece os nomes dos membros como entrada. 


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

### <a name="sample-index"></a>Índice de amostra

Um skillset é invocado por um indexante, e um indexante requer um índice. Uma representação complexa de campo no seu índice pode parecer o seguinte exemplo. 

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

### <a name="skill-input"></a>Entrada de habilidades

Um documento JSON que forneça uma entrada utilizável para esta habilidade **do Shaper** pode ser:

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

A habilidade **Shaper** gera um novo elemento chamado *textt analisado* com os elementos combinados de *texto* e *sentimento*. Esta saída está em conformidade com o esquema de índice. Será importado e indexado num índice de Pesquisa Cognitiva Azure.

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

## <a name="scenario-2-input-consolidation"></a>Cenário 2: consolidação de entradas

Noutro exemplo, imagine que em diferentes fases do processamento de gasodutos, extraiu o título de um livro, e títulos de capítulo em diferentes páginas do livro. Pode agora criar uma única estrutura composta por estas várias entradas.

A definição de habilidade **shaper** para este cenário pode parecer o seguinte exemplo:

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
Neste caso, o **Shaper** achata todos os títulos do capítulo para criar uma única matriz. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Cenário 3: consolidação de entradas a partir de contextos aninhados

Imagine que tem o título, capítulos e conteúdos de um livro e executou reconhecimento de entidades e frases-chave sobre os conteúdos e agora precisa agregar resultados das diferentes competências numa única forma com o nome do capítulo, entidades e frases-chave.

A definição de habilidade **shaper** para este cenário pode parecer o seguinte exemplo:

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
Neste caso, o **Shaper** cria um tipo complexo. Esta estrutura existe na memória. Se quiser guardá-lo para uma loja de [conhecimento,](knowledge-store-concept-intro.md)deve criar uma projeção na sua habilidade que defina características de armazenamento.

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

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Como utilizar tipos complexos](search-howto-complex-data-types.md)
+ [Arquivo de dados de conhecimento](knowledge-store-concept-intro.md)
+ [Criar uma loja de conhecimento em REST](knowledge-store-create-rest.md)