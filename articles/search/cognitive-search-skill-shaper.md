---
title: Habilidade de pesquisa cognitiva Modelador - Azure Search
description: Extrair metadados e informações estruturadas de dados não estruturados e formatá-los como um tipo complexo num pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 058b6c979346d9dcce36940432d0e222e919dba9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540833"
---
#   <a name="shaper-cognitive-skill"></a>Habilidade de cognitiva Modelador

O **Modelador** habilidade consolida várias entradas num [tipo complexo](search-howto-complex-data-types.md) que pode ser referenciado mais tarde no pipeline de melhoria. O **Modelador** habilidade permite-lhe criar uma estrutura essencialmente, definir o nome dos membros dessa estrutura e atribuir valores a cada membro. Exemplos de campos consolidados útil em cenários de pesquisa incluem a combinação de um nome próprio e apelido numa estrutura simples, cidade e estado numa estrutura simples, ou o nome e data de nascimento numa única estrutura para estabelecer a identidade exclusiva.

A versão de API determina a profundidade de formatação pode atingir. 

| Versão de API | Comportamentos de formatação | 
|-------------|-------------------|
| Versão de 2019-05-06-pré-visualização da REST API (o SDK do .NET não é suportado) | Objetos complexos, vários níveis de profundidade, de uma **Modelador** definição de habilidade. |
| 05 de 2019-06 * * (disponível em geral), 2017-11-11-pré-visualização| Objetos complexos, um nível de profundidade. Uma forma de múltiplos nível requer o encadeamento de vários passos Modelador em conjunto.|

Fornecido pelo `api-version=2019-05-06-Preview`, o **Modelador** habilidade ilustrado na [cenário 3](#nested-complex-types) adiciona um novo opcional *sourceContext* propriedade para a entrada. O *origem* e *sourceContext* propriedades são mutuamente exclusivas. Se a entrada é no contexto da habilidade, basta usar *origem*. Se a entrada for num *diferentes* contexto que o contexto de habilidade, utilize o *sourceContext*. O *sourceContext* exige que defina uma entrada aninhada com o elemento específico que está a ser tratado como a origem. 

Em resposta, para todas as versões de API, o nome de saída é sempre "output". Internamente, o pipeline pode mapear um nome diferente, como "analyzedText" conforme mostrado nos exemplos abaixo, mas a **Modelador** habilidade em si devolve "resultado" na resposta. Isso pode ser importante se a depuração plena documentos e observe as discrepâncias de nomenclatura ou se compilar uma habilidade personalizada e é estruturar a resposta por conta própria.

> [!NOTE]
> O **Modelador** habilidade não está vinculada a uma API dos serviços cognitivos e não lhe é cobrados usá-lo. Deve ainda [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md), no entanto, para substituir o **gratuito** opção de recursos que limita a um pequeno número de diário possível por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Cenário 1: os tipos complexos

Considere um cenário onde pretende criar uma estrutura chamada *analyzedText* que tem dois membros: *texto* e *sentimentos*, respectivamente. Num índice da Azure Search, é chamado de um campo pesquisável com várias parte um *tipo complexo* e, muitas vezes, é criado quando a origem de dados tem uma estrutura de complexa correspondente que mapeia para o mesmo.

No entanto, a outra abordagem para a criação de tipos complexos é por meio da **Modelador** habilidade. Ao incluir essa habilidade num conjunto de capacidades, as operações de dentro da memória durante o processamento do conjunto de capacidades podem enviar resultados formas de dados com as estruturas aninhadas, em seguida, podem ser mapeadas para um tipo complexo no seu índice. 

A seguinte definição de habilidades de exemplo fornece o membro nomes como entrada. 


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

Um conjunto de capacidades é invocado por um indexador e um indexador requer um índice. Uma representação de campo complexas no seu índice pode ter um aspeto semelhante ao seguinte exemplo. 

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

Um documento JSON de entrada fornecer entrada utilizável para isso **Modelador** habilidade poderia ser:

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

O **Modelador** habilidade gera um novo elemento chamado *analyzedText* com os elementos combinados de *texto* e *sentimentos*. Esta saída está em conformidade com o esquema de índice. Serão importado e indexado no índice da Azure Search.

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

Noutro exemplo, imagine que em diferentes fases do processamento de pipeline, ter extraiu o título de um livro e títulos do capítulo em diferentes páginas do livro. Agora pode criar uma estrutura única composta por estas várias entradas.

O **Modelador** definição de habilidades para este cenário poderá ter um aspeto semelhante ao seguinte exemplo:

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
Neste caso, o **Modelador** nivela todos os títulos de capítulo para criar uma única matriz. 

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

> [!NOTE]
> Aninhados estruturas, heterogenních doménách aplikace a [REST API versão 2019-05-06-pré-visualização](search-api-preview.md) podem ser usados num [arquivo de dados de conhecimento](knowledge-store-concept-intro.md) ou num índice da Azure Search.

Imagine que tem o título, capítulos e conteúdo de um livro e executou entidade frases de reconhecimento e a chave no conteúdo e agora precisa agregar os resultados de diferentes habilidades numa única forma com o nome do capítulo, entidades e expressões-chave.

O **Modelador** definição de habilidades para este cenário poderá ter um aspeto semelhante ao seguinte exemplo:

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
Neste caso, o **Modelador** cria um tipo complexo. Esta estrutura existe dentro da memória. Se quiser guardá-lo para um arquivo de dados de conhecimento, deve criar uma projeção no seu conjunto de capacidades que define as características de armazenamento.

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

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Como usar tipos complexos](search-howto-complex-data-types.md)
+ [Descrição geral do arquivo de dados de conhecimento](knowledge-store-concept-intro.md)
+ [Como começar com o arquivo de dados de conhecimento](knowledge-store-howto.md)