---
title: Habilidade cognitiva condicional
titleSuffix: Azure Cognitive Search
description: A habilidade condicional na Pesquisa Cognitiva Azure permite filtrar, criar incumprimentos e fundir valores numa definição de skillset.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f47ca56fa1b40422edeb0d4e11c24be6f60e49e5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666367"
---
# <a name="conditional-cognitive-skill"></a>Habilidade cognitiva condicional

A habilidade **condicional** permite que a Azure Cognitive Search cenários que requerem uma operação Boolean para determinar os dados para atribuir a uma saída. Estes cenários incluem filtragem, atribuição de um valor padrão e fusão de dados com base numa condição.

O pseudocódigo que se segue demonstra o que a habilidade condicional realiza:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Esta habilidade não está ligada a uma API dos Serviços Cognitivos Azure, e não és cobrado por usá-la. No entanto, deve ainda [anexar um recurso de Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para anular a opção de recurso "Livre" que o limita a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campos avaliados

Esta habilidade é especial porque os seus inputs são campos avaliados.

Os seguintes itens são valores válidos de uma expressão:

-   Caminhos de anotação (os caminhos das expressões devem ser delimitados por "$"(" e ")")
 <br/>
    Exemplos:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literal (cordas, números, verdadeiro, falso, nulo) <br/>
    Exemplos:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Expressões que utilizam operadores de comparação (==, !=, >=, >, <=, <) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expressões que utilizam operadores Boolean (&&, ||, !, ^) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expressões que utilizam operadores numéricos (+, -, \* /, %) <br/>
    Exemplos: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Como a habilidade condicional suporta a avaliação, pode usá-la em cenários de transformação menor. Por exemplo, consulte [a definição de habilidade 4](#transformation-example).

## <a name="skill-inputs"></a>Entradas de habilidades
As entradas são sensíveis a casos.

| Entrada   | Descrição |
|-------------|-------------|
| condição   | Esta entrada é um [campo avaliado](#evaluated-fields) que representa a condição para avaliar. Esta condição deve avaliar a um valor booleano *(verdadeiro* ou *falso).*   <br/>  Exemplos: <br/> "= verdadeiro" <br/> "= $(/documento/linguagem) ='fr'" <br/> "= $(/documento/páginas/ \* /linguagem) ==$(/documento/expectLanguage)" <br/> |
| quandoTrue    | Esta entrada é um [campo avaliado](#evaluated-fields) que representa o valor a devolver se a condição for avaliada como *verdadeira*. As cordas constantes devem ser devolvidas em aspas únicas (' e '). <br/>Valores da amostra: <br/> "= 'contrato'"<br/>"= $(/documento/contra-contratoType)" <br/> "= $(/documento/entidades/ \* )" <br/> |
| quando aFalse   | Esta entrada é um [campo avaliado](#evaluated-fields) que representa o valor a devolver se a condição for avaliada em *falso*. <br/>Valores da amostra: <br/> "= 'contrato'"<br/>"= $(/documento/contra-contratoType)" <br/> "= $(/documento/entidades/ \* )" <br/>

## <a name="skill-outputs"></a>Saídas de competências
Há uma única saída que é simplesmente chamada de "saída". Devolve o valor *quando aFale* se a condição é falsa ou *quando a* condição é verdadeira.

## <a name="examples"></a>Exemplos

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Definição de habilidade de amostra 1: Filtrar documentos para devolver apenas documentos franceses

A seguinte saída devolve uma série de frases ("/document/frenchSentences") se a língua do documento for francesa. Se a língua não é francês, o valor é definido para *nulo.*

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
Se "/document/frenchSentences" for usado como *contexto* de outra habilidade, essa habilidade só funciona se "/document/frenchSentences" não for definido como *nulo*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Definição de habilidade de amostra 2: Definir um valor padrão para um valor que não existe

A seguinte saída cria uma anotação ("/document/languageWithDefault") que é definida para a linguagem do documento ou para "es" se a língua não estiver definida.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Definição de habilidade de amostra 3: Fundir valores de dois campos em um

Neste exemplo, algumas frases têm uma propriedade *francesa.* Sempre que a *propriedade frenchSentiment* for nula, queremos usar o valor *do direito de inscrição.* Atribuímos a saída a um membro chamado *sentimento* ("/document/sentences/*/sentiment").

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Definição de habilidade de amostra 4: Transformação de dados num único campo

Neste exemplo, recebemos um *sentimento* que está entre 0 e 1. Queremos transformá-lo para estar entre -1 e 1. Podemos usar a habilidade condicional para fazer esta pequena transformação.

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
Alguns parâmetros são avaliados, por isso é preciso ter especialmente cuidado para seguir o padrão documentado. As expressões devem começar com um sinal de iguais. Um caminho deve ser delimitado por "$". Certifique-se de colocar cordas em marcas únicas de aspas. Isso ajuda o avaliador a distinguir entre cordas e caminhos e operadores reais. Além disso, certifique-se de colocar o espaço branco em torno dos operadores (por exemplo, um "*" num caminho significa algo diferente do multiplicar).


## <a name="next-steps"></a>Passos seguintes

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
