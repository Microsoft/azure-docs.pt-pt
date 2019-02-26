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
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c55783e9b209a1280a21edca34b75e72481f4cb6
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807002"
---
#   <a name="shaper-cognitive-skill"></a>Habilidade de cognitiva Modelador

O **Modelador** habilidade consolida várias entradas num tipo complexo que pode ser referenciado mais tarde no pipeline de melhoria. O **Modelador** habilidade permite-lhe criar uma estrutura essencialmente, definir o nome dos membros dessa estrutura e atribuir valores a cada membro. Exemplos de campos consolidados útil em cenários de pesquisa incluem a combinação de um nome próprio e apelido numa estrutura simples, cidade e estado numa estrutura simples, ou o nome e data de nascimento numa única estrutura para estabelecer a identidade exclusiva.

Por padrão, essa técnica oferece suporte a objetos que estão um nível de profundidade. Para obter objetos mais complexos, pode encadear vários **Modelador** passos.

Em resposta, o nome de saída é sempre "output". Internamente, o pipeline pode mapear um nome diferente, como "analyzedText" nos exemplos abaixo de "saída", mas a **Modelador** habilidade em si devolve "resultado" na resposta. Isso pode ser importante se a depuração plena documentos e observe as discrepâncias de nomenclatura ou se compilar uma habilidade personalizada e é estruturar a resposta por conta própria.

> [!NOTE]
> Essa habilidade não está vinculada a uma API dos serviços cognitivos e não lhe é cobrados usá-lo. Deve ainda [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md), no entanto, para substituir o **gratuito** opção de recursos que limita a um pequeno número de diário possível por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Exemplo 1: tipos complexos

Considere um cenário onde pretende criar uma estrutura chamada *analyzedText* que tem dois membros: *texto* e *sentimentos*, respectivamente. No Azure Search, é chamado de um campo pesquisável com várias parte um *tipo complexo*, e ainda não é suportado de imediato. Nesta pré-visualização, uma **Modelador** habilidade pode ser utilizada para gerar os campos de um tipo complexo no seu índice. 

O exemplo a seguir fornece o membro nomes como entrada. A estrutura de saída (o campo complexa no Azure Search) é especificada através de *targetName*. 


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

### <a name="sample-input"></a>Entrada de exemplo
Um documento JSON fornecer entrada utilizável para isso **Modelador** habilidade poderia ser:

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


### <a name="sample-output"></a>Resultado da amostra
O **Modelador** habilidade gera um novo elemento chamado *analyzedText* com os elementos combinados de *texto* e *sentimentos*. 

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

## <a name="sample-2-input-consolidation"></a>Exemplo 2: consolidação de entrada

Noutro exemplo, imagine que em diferentes fases do processamento de pipeline, ter extraiu o título de um livro e títulos do capítulo em diferentes páginas do livro. Agora pode criar uma estrutura única composta por estas várias entradas.

A definição de habilidades Modelador para este cenário poderá ter um aspeto semelhante ao seguinte exemplo:

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
            "source": "/document/content/pages/*/chapterTitles/*"
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

### <a name="sample-output"></a>Resultado da amostra
Neste caso, o Modelador nivela todos os títulos de capítulo para criar uma única matriz. 

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

## <a name="see-also"></a>Consulte também

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)

