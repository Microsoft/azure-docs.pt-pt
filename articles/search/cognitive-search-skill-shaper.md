---
title: Competência cognitiva do formulador
titleSuffix: Azure Cognitive Search
description: Extrair metadados e informação estruturada a partir de dados não estruturados e moldá-lo como um tipo complexo num oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75754127"
---
# <a name="shaper-cognitive-skill"></a>Competência cognitiva do formulador

A habilidade **Shaper** consolida várias inputs num [tipo complexo](search-howto-complex-data-types.md) que pode ser referenciado mais tarde no pipeline de enriquecimento. A habilidade **Shaper** permite-lhe essencialmente criar uma estrutura, definir o nome dos membros dessa estrutura e atribuir valores a cada membro. Exemplos de campos consolidados úteis em cenários de pesquisa incluem combinar um primeiro e último nome numa única estrutura, cidade e estado numa única estrutura, ou nome e data de nascimento numa única estrutura para estabelecer uma identidade única.

Adicionalmente, a habilidade **Shaper** ilustrada no [cenário 3](#nested-complex-types) adiciona uma propriedade de *origem opcionalContexto* à entrada. As propriedades *de origem* e *origemContexto* são mutuamente exclusivas. Se a entrada estiver no contexto da habilidade, utilize simplesmente a *fonte*. Se a entrada estiver num contexto *diferente* do contexto de habilidade, utilize o *contexto de origem*. O *SourceContext* requer que defina uma entrada aninhada com o elemento específico a ser abordado como a fonte. 

O nome de saída é sempre "output". Internamente, o pipeline pode mapear um nome diferente, como "texto analisado" como mostram os exemplos abaixo, mas a habilidade **shaper** em si devolve "output" na resposta. Isto pode ser importante se estiver a depurar documentos enriquecidos e notar a discrepância de nomeação, ou se você mesmo constrói uma habilidade personalizada e está estruturando a resposta.

> [!NOTE]
> A habilidade **shaper** não está ligada a uma API de Serviços Cognitivos e você não é cobrado por usá-lo. No entanto, deve ainda [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md)para anular a opção de recursos **gratuitos** que o limita a um pequeno número de enriquecimentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Cenário 1: tipos complexos

Considere um cenário em que pretende criar uma estrutura chamada *texto analisado* que tenha dois membros: *texto* e *sentimento,* respectivamente. Num índice, um campo pesquisável em várias partes é chamado *de tipo complexo* e é frequentemente criado quando os dados de origem têm uma estrutura complexa correspondente que lhe mapeia.

No entanto, outra abordagem para criar tipos complexos é através da habilidade **Shaper.** Ao incluir esta habilidade num skillset, as operações de memória durante o processamento de skillset podem obter formas de dados com estruturas aninhadas, que podem então ser mapeadas para um tipo complexo no seu índice. 

A definição de habilidade de exemplo seguinte fornece os nomes dos membros como entrada. 


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

### <a name="sample-index"></a>Índice de amostras

Um skillset é invocado por um indexante, e um indexante requer um índice. Uma representação de campo complexa no seu índice pode parecer o seguinte exemplo. 

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

Um documento jSON que fornece uma entrada utilizável para esta habilidade **Shaper** pode ser:

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

A habilidade **Shaper** gera um novo elemento chamado *texto analisado* com os elementos combinados de *texto* e *sentimento*. Esta saída está em conformidade com o esquema do índice. Será importado e indexado num índice de Pesquisa Cognitiva Azure.

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

## <a name="scenario-2-input-consolidation"></a>Cenário 2: consolidação do input

Noutro exemplo, imagine que em diferentes fases do processamento de gasodutos, extraiu o título de um livro, e capítulo títulos em diferentes páginas do livro. Agora poderia criar uma única estrutura composta por estas várias inputs.

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Cenário 3: consolidação de entrada a partir de contextos aninhados

Imagine que tem o título, capítulos e conteúdos de um livro e executou o reconhecimento de entidades e frases-chave sobre os conteúdos e agora precisa agregar resultados das diferentes competências numa única forma com o nome do capítulo, entidades e frases-chave.

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
Neste caso, o **Shaper** cria um tipo complexo. Esta estrutura existe na memória. Se quiser guardá-lo para uma loja de [conhecimentos,](knowledge-store-concept-intro.md)deve criar uma projeção no seu skillset que defina as características de armazenamento.

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

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Como usar tipos complexos](search-howto-complex-data-types.md)
+ [Arquivo de dados de conhecimento (pré-visualização)](knowledge-store-concept-intro.md)
+ [Criar uma loja de conhecimentos em REST](knowledge-store-create-rest.md)