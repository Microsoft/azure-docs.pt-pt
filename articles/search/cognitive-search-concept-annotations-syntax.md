---
title: Entradas de referência e saídas no cognitivos pesquisa pipelines - Azure Search
description: Explica a sintaxe de anotação e como fazer referência a uma anotação nas entradas e saídas de um conjunto de capacidades num pipeline no Azure Search pesquisa cognitiva.
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
ms.openlocfilehash: bfb8f5ca9b4d204b7a5efdc1b54a0fdd150e5ed6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749618"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Como fazer referência a anotações num conjunto de capacidades de pesquisa cognitiva

Neste artigo, saiba como fazer referência a anotações nas definições de habilidade, com exemplos para ilustrar os vários cenários. Como o conteúdo de um documento flui através de um conjunto de habilidades, obtém enriquecida com anotações. Anotações podem ser utilizadas como entradas para ainda mais a jusante enriquecimento ou mapeadas para um campo de saída num índice. 
 
Exemplos neste artigo são baseiam o *conteúdo* campo gerado automaticamente pelo [indexadores de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) como parte do fase de abertura do documento. Quando nos Referimos a documentos a partir de um contentor de BLOBs, utilize um formato como `"/document/content"`, em que o *conteúdo* campo faz parte dos *documento*. 

## <a name="background-concepts"></a>Conceitos de segundo plano

Antes de examinar a sintaxe, vamos rever alguns conceitos importantes a compreender melhor os exemplos fornecidos neste artigo.

| Termo | Descrição |
|------|-------------|
| Documento plena | Um documento plena é uma estrutura interna criado e utilizado pelo pipeline para conter todas as anotações relacionadas a um documento. Considere um documento plena como uma árvore de anotações. Em geral, uma anotação criada a partir de uma anotação anterior torna-se o seu filho.<p/>Só existem documentos plena durante o período de execução do conjunto de capacidades. Assim que o conteúdo está mapeado para o índice de pesquisa, o documento plena já não é necessária. Embora não interage com plena documentos diretamente, é útil ter um modelo mental de documentos durante a criação de um conjunto de capacidades. |
| Contexto de melhoria | O contexto no qual o enriquecimento ocorre, em termos de que é enriquecido elemento. Por predefinição, o contexto de melhoria é no `"/document"` nível, no âmbito de documentos individuais. Quando uma habilidade é executada, as saídas dessa habilidade de se tornar [propriedades do contexto definido](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exemplo 1: Referência de anotação simples

Armazenamento de Blobs do Azure, suponha que tem uma variedade de ficheiros que contêm referências a nomes de pessoas que pretende extrair usando o reconhecimento de entidades. Na definição de habilidades abaixo, `"/document/content"` é a representação textual de todo o documento, e "pessoas" é uma extração de nomes completos para entidades identificados como pessoas.

Uma vez que o contexto predefinido é `"/document"`, a lista de pessoas agora pode ser referenciada como `"/document/people"`. Nesse caso específico `"/document/people"` é uma anotação, que pode agora ser mapeada para um campo num índice ou utilizada em outra habilidade no mesmo conjunto de capacidades.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Exemplo 2: Uma matriz dentro de um documento de referência

Este exemplo baseia-se na anterior, que lhe mostram como invocar um passo de enriquecimento várias vezes durante o mesmo documento. Suponha que o exemplo anterior gerado uma matriz de cadeias de caracteres com 10 nomes de pessoas a partir de um único documento. Passo seguinte razoável pode ser uma melhoria do segundo que extrai o sobrenome de um nome completo. Como há 10 nomes, pretende que este passo a ser chamado 10 vezes neste documento, uma vez para cada pessoa. 

Para invocar o número certo de iterações, definir o contexto como `"/document/people/*"`, em que o asterisco (`"*"`) representa todos os nós no documento plena como descendentes de `"/document/people"`. Embora essa habilidade é definida apenas depois da matriz de habilidades, é chamado para cada membro dentro do documento até que todos os membros são processados.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Quando as anotações são matrizes ou coleções de cadeias de caracteres, pode querer segmentar membros específicos, em vez da matriz como um todo. O exemplo acima gera uma anotação chamada `"last"` em cada nó representado por contexto. Se desejar referir-se para esta família de anotações, poderia usar a sintaxe `"/document/people/*/last"`. Se desejar referir-se a uma anotação específica, poderia usar um índice explícito: `"/document/people/1/last`"para referenciar o sobrenome da pessoa primeiro identificado no documento. Tenha em atenção que, nessa sintaxe matrizes são "0 indexados".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exemplo 3: Membros de referência dentro de uma matriz

Às vezes precisa agrupar todas as anotações de um determinado tipo passá-los para uma determinada habilidade. Considere uma habilidade personalizada hipotética que identifica o sobrenome de segurança mais comuns de todos os nomes de última extraído no exemplo 2. Para fornecer apenas os último nomes para a habilidade de personalizado, especifique o contexto como `"/document"` e a entrada como `"/document/people/*/lastname"`.

Tenha em atenção que a cardinalidade de `"/document/people/*/lastname"` é maior do que o documento. Talvez haja 10 nós lastname enquanto houver apenas um nó de documento para este documento. Nesse caso, o sistema irá criar automaticamente uma matriz de `"/document/people/*/lastname"` que contém todos os elementos no documento.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Consulte também
+ [Como integrar uma habilidade personalizada num pipeline de melhoria](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar conjunto de capacidades (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos plena para um índice](cognitive-search-output-field-mapping.md)
