---
title: Inputs e saídas de referência em skillsets
titleSuffix: Azure Cognitive Search
description: Explica a sintaxe de anotação e como referenciar uma anotação nas inputs e saídas de um skillset num oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e27f61239c0631fb248217777a311b13ee48a3f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113863"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Como referenciar anotações num conjunto de habilidades de Pesquisa Cognitiva Azure

Neste artigo, aprende-se a referir anotações em definições de competências, utilizando exemplos para ilustrar vários cenários. À medida que o conteúdo de um documento flui através de um conjunto de habilidades, ele é enriquecido com anotações. As anotações podem ser usadas como inputs para um enriquecimento a jusante mais aprofundado, ou mapeadas para um campo de saída num índice. 
 
Exemplos neste artigo baseiam-se no campo de *conteúdo* gerado automaticamente pelos [indexadores Do Blob Azure](search-howto-indexing-azure-blob-storage.md) como parte da fase de rutura do documento. Quando se referir a documentos de um recipiente `"/document/content"`Blob, utilize um formato como, onde o campo de *conteúdo* faz parte do *documento*. 

## <a name="background-concepts"></a>Conceitos de fundo

Antes de rever a sintaxe, vamos revisitar alguns conceitos importantes para entender melhor os exemplos fornecidos mais tarde neste artigo.

| Termo | Descrição |
|------|-------------|
| Documento Enriquecido | Um documento enriquecido é uma estrutura interna criada e utilizada pelo oleoduto para manter todas as anotações relacionadas com um documento. Pense num documento enriquecido como uma árvore de anotações. Geralmente, uma anotação criada a partir de uma anotação anterior torna-se a sua criança.<p/>Os documentos enriquecidos só existem durante a execução de habilidades. Uma vez mapeado o conteúdo para o índice de pesquisa, o documento enriquecido já não é necessário. Embora não interaja diretamente com documentos enriquecidos, é útil ter um modelo mental dos documentos ao criar uma habilidade. |
| Contexto de Enriquecimento | O contexto em que o enriquecimento ocorre, em termos de qual elemento é enriquecido. Por padrão, o contexto `"/document"` de enriquecimento está ao nível, ao nível dos documentos individuais. Quando uma habilidade corre, as saídas dessa habilidade tornam-se [propriedades do contexto definido.](#example-2)|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exemplo 1: Referência simples de anotação

No armazenamento da Azure Blob, suponha que tenha uma variedade de ficheiros que contêm referências aos nomes das pessoas que pretende extrair usando o reconhecimento de entidades. Na definição de `"/document/content"` competência abaixo, está a representação textual de todo o documento, e "pessoas" é uma extração de nomes completos para entidades identificadas como pessoas.

Como o contexto `"/document"`padrão é, a lista de `"/document/people"`pessoas pode agora ser referenciada como . Neste caso `"/document/people"` específico é uma anotação, que poderia agora ser mapeada para um campo num índice, ou usada em outra habilidade no mesmo skillset.

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

Este exemplo baseia-se no anterior, mostrando-lhe como invocar um passo de enriquecimento várias vezes sobre o mesmo documento. Assumindo que o exemplo anterior gerou uma série de cordas com 10 nomes de pessoas de um único documento. Um próximo passo razoável pode ser um segundo enriquecimento que extrai o último nome de um nome completo. Como há 10 nomes, quer que este passo seja chamado 10 vezes neste documento, uma vez para cada pessoa. 

Para invocar o número certo de iterações, estabeleça o contexto como, `"/document/people/*"`onde o asterisco`"*"`representa todos `"/document/people"`os nós no documento enriquecido como descendentes de . Embora esta habilidade só seja definida uma vez na gama de competências, é chamada para cada membro dentro do documento até que todos os membros sejam processados.

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

Quando as anotações são matrizes ou coleções de cordas, é melhor direcionar membros específicos em vez da matriz como um todo. O exemplo acima gera uma `"last"` anotação chamada sob cada nó representado pelo contexto. Se quiser referir-se a esta família de anotações, `"/document/people/*/last"`pode usar a sintaxe. Se quiser referir-se a uma anotação em particular, pode utilizar um índice explícito: `"/document/people/1/last`" para referir o último nome da primeira pessoa identificada no documento. Note que neste conjunto de sintaxe são "0 indexados".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exemplo 3: Membros de referência dentro de uma matriz

Às vezes é preciso agrupar todas as anotações de um tipo específico para as passar a uma determinada habilidade. Considere uma habilidade personalizada hipotética que identifica o apelido mais comum de todos os apelidos extraídos no Exemplo 2. Para fornecer apenas os últimos nomes `"/document"` à habilidade personalizada, especifique o contexto como e a entrada como `"/document/people/*/lastname"`.

Note que a `"/document/people/*/lastname"` cardinalidade de é maior do que a do documento. Pode haver 10 nós de apelido enquanto só há um nó documental para este documento. Nesse caso, o sistema criará automaticamente `"/document/people/*/lastname"` uma série de contendo todos os elementos do documento.

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
+ [Como integrar uma habilidade personalizada num oleoduto de enriquecimento](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos para um índice](cognitive-search-output-field-mapping.md)
