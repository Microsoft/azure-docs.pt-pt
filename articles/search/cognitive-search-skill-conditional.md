---
title: Habilidade cognitiva condicional
titleSuffix: Azure Cognitive Search
description: A habilidade condicional na Pesquisa Cognitiva Azure permite a filtragem, criação de incumprimentos e fusão de valores numa definição de skillset.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792037"
---
# <a name="conditional-cognitive-skill"></a>Habilidade cognitiva condicional

A habilidade **condicional** permite cenários de pesquisa cognitiva Azure que requerem uma operação Boolean para determinar os dados para atribuir a uma saída. Estes cenários incluem filtragem, atribuição de um valor predefinido e fusão de dados com base numa condição.

O pseudocódigo que se segue demonstra o que a habilidade condicional consegue:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Esta habilidade não está ligada a uma API dos Serviços Cognitivos Azure, e não é cobrado por usá-lo. No entanto, deve ainda [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para anular a opção de recurso "Grátis" que o limita a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campos avaliados

Esta habilidade é especial porque as suas inputs são áreas avaliadas.

Os seguintes itens são valores válidos de expressão:

-   Caminhos de anotação (caminhos em expressões devem ser delimitados por "$(" e ")")
 <br/>
    Exemplos:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literals (cordas, números, verdadeiro, falso, nulo) <br/>
    Exemplos:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Expressões que utilizam operadores de comparação (==, ==, >=, >, <=, <) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expressões que usam operadores booleanos (&&, [...], ^) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expressões que utilizam operadores numéricos (+, -, \*/, %) <br/>
    Exemplos: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Como a habilidade condicional suporta a avaliação, pode usá-la em cenários de transformação menor. Por exemplo, ver [definição de habilidade 4](#transformation-example).

## <a name="skill-inputs"></a>Inputs de habilidade
As inputs são sensíveis aos casos.

| Input   | Descrição |
|-------------|-------------|
| condição   | Esta entrada é um [campo avaliado](#evaluated-fields) que representa a condição de avaliar. Esta condição deve avaliar para um valor booleano *(verdadeiro* ou *falso).*   <br/>  Exemplos: <br/> "= verdadeiro" <br/> "= $(/documento/língua) =='fr'" <br/> "= $(/documento/páginas/\*/idioma) == $(/documento/linguagem esperada)" <br/> |
| quandoTrue    | Esta entrada é um [campo avaliado](#evaluated-fields) que representa o valor a devolver se a condição for avaliada de *forma verdadeira*. As cordas de constantes devem ser devolvidas em aspas únicas (' e '). <br/>Valores da amostra: <br/> "= 'contrato'"<br/>"= $(/documento/contractType)" <br/> "= $(/documento/entidades/\*)" <br/> |
| quando Falso   | Esta entrada é um [campo avaliado](#evaluated-fields) que representa o valor a devolver se a condição for avaliada como *falsa*. <br/>Valores da amostra: <br/> "= 'contrato'"<br/>"= $(/documento/contractType)" <br/> "= $(/documento/entidades/\*)" <br/>

## <a name="skill-outputs"></a>Saídas de habilidades
Há uma única saída que é simplesmente chamada de "saída". Devolve o valor *quando falso* se a condição for falsa ou *quando verdadeira* se a condição for verdadeira.

## <a name="examples"></a>Exemplos

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Definição de habilidade da amostra 1: Filtrar documentos para devolver apenas documentos franceses

A saída seguinte devolve um conjunto de frases ("/documento/francêsFrases") se a língua do documento for francesa. Se a língua não for francesa, o valor está definido para *nulo.*

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Se "/documento/francêsSentences" for usado como *contexto* de outra habilidade, essa habilidade só funciona se "/documento/francêsSentences" não for em *otonuda*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Definição de habilidade da amostra 2: Definir um valor predefinido para um valor que não existe

A saída seguinte cria uma anotação ("/documento/idiomaWithDefault") que é definida para o idioma do documento ou para "es" se a língua não estiver definida.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Definição de habilidade da amostra 3: Fundir valores de dois campos em um

Neste exemplo, algumas frases têm uma propriedade *francesa Sentiment.* Sempre que a propriedade *francesa Sentiment* é nula, queremos usar o valor *inglêsSentiment.* Atribuímos a saída a um membro chamado *sentimento* ("/documento/sentimento/*/sentimento").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Exemplo de transformação
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Definição de habilidade da amostra 4: Transformação de dados num único campo

Neste exemplo, recebemos um *sentimento* que está entre 0 e 1. Queremos transformá-lo entre -1 e 1. Podemos usar a habilidade condicional para fazer esta pequena transformação.

Neste exemplo, não usamos o aspeto condicional da habilidade porque a condição é sempre *verdadeira.*

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Considerações especiais
Alguns parâmetros são avaliados, por isso você precisa ter especial cuidado para seguir o padrão documentado. Expressões devem começar com um sinal igual. Um caminho deve ser delimitado por "$(" e ")". Certifique-se de colocar cordas em aspas únicas. Isso ajuda o avaliador a distinguir entre cordas e caminhos reais e operadores. Além disso, certifique-se de colocar espaço branco em torno dos operadores (por exemplo, um "*" num caminho significa algo diferente do que multiplicar).


## <a name="next-steps"></a>Passos seguintes

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
