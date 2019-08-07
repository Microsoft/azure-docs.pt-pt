---
title: Referenciar entradas e saídas em pipelines de pesquisa cognitiva-Azure Search
description: Explica a sintaxe da anotação e como fazer referência a uma anotação nas entradas e saídas de um conconhecimento em um pipeline de pesquisa cognitiva no Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 1868e9fd3a7dde5d6302753986019f481a577007
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841295"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Como fazer referência a anotações em um congrau de conhecimento de pesquisa cognitiva

Neste artigo, você aprenderá a fazer referência a anotações em definições de habilidades, usando exemplos para ilustrar vários cenários. Como o conteúdo de um documento flui por um conjunto de habilidades, ele é aprimorado com anotações. As anotações podem ser usadas como entradas para aprimoramentos posteriores de downstream ou mapeadas para um campo de saída em um índice. 
 
Os exemplos neste artigo baseiam-se no campo de *conteúdo* gerado automaticamente pelos indexadores de [blob do Azure](search-howto-indexing-azure-blob-storage.md) como parte da fase de violação do documento. Ao fazer referência a documentos de um contêiner de BLOB, use um formato como `"/document/content"`, em que o campo *conteúdo* faz parte do *documento*. 

## <a name="background-concepts"></a>Conceitos de segundo plano

Antes de examinar a sintaxe, Vamos revisitar alguns conceitos importantes para entender melhor os exemplos fornecidos posteriormente neste artigo.

| Termo | Descrição |
|------|-------------|
| Documento aprimorado | Um documento aprimorado é uma estrutura interna criada e usada pelo pipeline para manter todas as anotações relacionadas a um documento. Imagine um documento aprimorado como uma árvore de anotações. Em geral, uma anotação criada a partir de uma anotação anterior se torna seu filho.<p/>Os documentos aprimorados só existem durante a execução do conconhecimento. Depois que o conteúdo é mapeado para o índice de pesquisa, o documento aprimorado não é mais necessário. Embora você não interaja com documentos aprimorados diretamente, é útil ter um modelo mental dos documentos ao criar um Skill. |
| Contexto de enriquecimento | O contexto no qual ocorre o enriquecimento, em termos de qual elemento é aprimorado. Por padrão, o contexto de enriquecimento está no `"/document"` nível, com escopo para documentos individuais. Quando uma habilidade é executada, as saídas dessa habilidade se tornam [Propriedades do contexto definido](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exemplo 1: Referência de anotação simples

No armazenamento de BLOBs do Azure, suponha que você tenha uma variedade de arquivos contendo referências a nomes de pessoas que você deseja extrair usando o reconhecimento de entidade. Na definição de habilidade abaixo, `"/document/content"` é a representação textual do documento inteiro e "pessoas" é uma extração de nomes completos para entidades identificadas como pessoas.

Como o contexto padrão é `"/document"`, a lista de pessoas agora pode ser referenciada `"/document/people"`como. Nesse caso `"/document/people"` específico, há uma anotação, que agora pode ser mapeada para um campo em um índice ou usada em outra habilidade no mesmo contratador de qualificações.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

## <a name="example-2-reference-an-array-within-a-document"></a>Exemplo 2: Referenciar uma matriz dentro de um documento

Este exemplo se baseia no anterior, mostrando como invocar uma etapa de enriquecimento várias vezes ao longo do mesmo documento. Suponha que o exemplo anterior gerou uma matriz de cadeias de caracteres com 10 nomes de pessoas de um único documento. Uma próxima etapa razoável pode ser um segundo enriquecimento que extrai o sobrenome de um nome completo. Como há 10 nomes, você deseja que essa etapa seja chamada 10 vezes neste documento, uma vez para cada pessoa. 

Para invocar o número certo de iterações, defina o contexto `"/document/people/*"`como, em que o`"*"`asterisco () representa todos os nós no documento aprimorado como descendentes de `"/document/people"`. Embora essa habilidade seja definida apenas uma vez na matriz de habilidades, ela é chamada para cada membro dentro do documento até que todos os membros sejam processados.

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

Quando as anotações são matrizes ou coleções de cadeias de caracteres, você pode querer direcionar membros específicos em vez da matriz como um todo. O exemplo acima gera uma anotação chamada `"last"` em cada nó representado pelo contexto. Se você quiser fazer referência a essa família de anotações, poderá usar a sintaxe `"/document/people/*/last"`. Se você quiser fazer referência a uma anotação específica, poderá usar um índice explícito: `"/document/people/1/last`"para referenciar o sobrenome da primeira pessoa identificada no documento. Observe que nessas matrizes de sintaxe são "0 indexado".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exemplo 3: Membros de referência em uma matriz

Às vezes, você precisa agrupar todas as anotações de um tipo específico para passá-las para uma determinada habilidade. Considere uma habilidade personalizada hipotética que identifica o sobrenome mais comum de todos os sobrenomes extraídos no exemplo 2. Para fornecer apenas os sobrenomes à habilidade personalizada, especifique o contexto como `"/document"` e a entrada como `"/document/people/*/lastname"`.

Observe que a cardinalidade de `"/document/people/*/lastname"` é maior do que a do documento. Pode haver 10 nós LastName, enquanto há apenas um nó de documento para este documento. Nesse caso, o sistema criará automaticamente uma matriz de `"/document/people/*/lastname"` contendo todos os elementos no documento.

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
+ [Como integrar uma habilidade personalizada em um pipeline de enriquecimento](cognitive-search-custom-skill-interface.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Criar conconhecimento (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos aprimorados para um índice](cognitive-search-output-field-mapping.md)
