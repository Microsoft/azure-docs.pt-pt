---
title: Entradas e saídas de referência em skillsets
titleSuffix: Azure Cognitive Search
description: Explica a sintaxe de anotação e como fazer referência a uma anotação nas entradas e saídas de um skillset num pipeline de enriquecimento de IA em Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03431d861ca6d469b894e45c36fe2a3d7904c3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935539"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Como fazer referência a anotações num skillset de pesquisa cognitiva Azure

Neste artigo, aprende-se a referir anotações em definições de habilidades, utilizando exemplos para ilustrar vários cenários. À medida que o conteúdo de um documento flui através de um conjunto de habilidades, ele é enriquecido com anotações. As anotações podem ser usadas como entradas para um maior enriquecimento a jusante, ou mapeadas para um campo de saída num índice. 
 
Exemplos neste artigo baseiam-se no campo de *conteúdo* gerado automaticamente pelos [indexantes Azure Blob](search-howto-indexing-azure-blob-storage.md) como parte da fase de rachamento do documento. Quando se referir a documentos de um recipiente Blob, utilize um formato `"/document/content"` como, em que o campo de *conteúdo* faz parte do *documento.* 

## <a name="background-concepts"></a>Conceitos de fundo

Antes de rever a sintaxe, vamos revisitar alguns conceitos importantes para entender melhor os exemplos fornecidos mais tarde neste artigo.

| Termo | Descrição |
|------|-------------|
| Documento Enriquecido | Um documento enriquecido é uma estrutura interna criada e usada pelo oleoduto para conter todas as anotações relacionadas com um documento. Pense num documento enriquecido como uma árvore de anotações. Geralmente, uma anotação criada a partir de uma anotação anterior torna-se o seu filho.<p/>Os documentos enriquecidos só existem durante a execução do skillset. Uma vez que o conteúdo é mapeado para o índice de pesquisa, o documento enriquecido já não é necessário. Embora não interaja diretamente com documentos enriquecidos, é útil ter um modelo mental dos documentos ao criar uma habilidade. |
| Contexto de enriquecimento | O contexto em que o enriquecimento ocorre, em termos de qual elemento é enriquecido. Por padrão, o contexto de enriquecimento encontra-se ao `"/document"` nível, a nível dos documentos individuais. Quando uma habilidade corre, as saídas dessa habilidade tornam-se [propriedades do contexto definido.](#example-2)|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exemplo 1: Referência simples de anotação

No armazenamento da Azure Blob, suponha que tem uma variedade de ficheiros que contêm referências aos nomes das pessoas que pretende extrair usando o reconhecimento de entidades. Na definição de competência abaixo, `"/document/content"` está a representação textual de todo o documento, e "pessoas" é uma extração de nomes completos para entidades identificadas como pessoas.

Como o contexto padrão `"/document"` é, a lista de pessoas pode agora ser referenciada como `"/document/people"` . Neste caso específico `"/document/people"` é uma anotação, que pode agora ser mapeada para um campo num índice, ou usada em outra habilidade na mesma habilidade.

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

## <a name="example-2-reference-an-array-within-a-document"></a>Exemplo 2: Referência de uma matriz dentro de um documento

Este exemplo baseia-se no anterior, mostrando-lhe como invocar um passo de enriquecimento várias vezes sobre o mesmo documento. Assuma que o exemplo anterior gerou uma série de cordas com 10 nomes de pessoas de um único documento. Um próximo passo razoável pode ser um segundo enriquecimento que extrai o último nome de um nome completo. Como há 10 nomes, quer que este passo seja chamado 10 vezes neste documento, uma para cada pessoa. 

Para invocar o número certo de iterações, definir o contexto como `"/document/people/*"` , onde o asterisco representa `"*"` todos os nós no documento enriquecido como descendentes de `"/document/people"` . Embora esta habilidade seja definida apenas uma vez no conjunto de competências, é chamado para cada membro dentro do documento até que todos os membros sejam processados.

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

Quando as anotações são matrizes ou coleções de cordas, é possível que queira direcionar membros específicos em vez da matriz como um todo. O exemplo acima gera uma anotação chamada `"last"` sob cada nó representado pelo contexto. Se quiser consultar esta família de anotações, pode utilizar a `"/document/people/*/last"` sintaxe. Se quiser consultar uma anotação específica, pode utilizar um índice explícito: `"/document/people/1/last` " para fazer referência ao último nome da primeira pessoa identificada no documento. Note que neste conjunto de sintaxe os conjuntos são "0 indexados".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exemplo 3: Membros de referência dentro de um conjunto

Às vezes é preciso agrupar todas as anotações de um tipo específico para as passar a uma determinada habilidade. Considere uma habilidade hipotética personalizada que identifica o apelido mais comum de todos os apelidos extraídos no Exemplo 2. Para fornecer apenas os últimos nomes à habilidade personalizada, especifique o contexto como `"/document"` e a entrada como `"/document/people/*/lastname"` .

Note que a cardinalidade `"/document/people/*/lastname"` é maior do que a do documento. Pode haver 10 nós de último nome enquanto há apenas um nó de documento para este documento. Nesse caso, o sistema criará automaticamente uma matriz de  `"/document/people/*/lastname"` contenção de todos os elementos do documento.

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



## <a name="see-also"></a>Ver também
+ [Como integrar uma habilidade personalizada num oleoduto de enriquecimento](cognitive-search-custom-skill-interface.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos para um índice](cognitive-search-output-field-mapping.md)