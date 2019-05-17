---
title: Habilidade de pesquisa cognitiva condicional (Azure Search) | Documentos da Microsoft
description: A habilidade de condicional permite a filtragem, a criação de padrões e a mesclagem de valores.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791506"
---
#   <a name="conditional-skill"></a>Habilidade condicional

O *habilidade condicional* permite cenários de pesquisa do Azure que necessitam de uma operação booleana para determinar os dados para atribuir a um resultado. Estes cenários incluem a filtragem, atribuindo um valor predefinido e a mesclagem de dados com base numa condição.

O pseudocódigo a seguir demonstra o que faz a habilidade de condicional:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Essa habilidade não está vinculada a uma API de serviços cognitivos do Azure e não é cobrada para o utilizar. No entanto, deve ainda [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso de "Gratuito" que limita a um pequeno número de possível por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campos avaliados

Essa habilidade é especial porque suas entradas estiverem campos avaliados.

Os seguintes itens são valores válidos de uma expressão:

-   Caminhos de anotação (caminhos em expressões têm de ser delimitados por "$(" and ")")
 <br/>
    Exemplos:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literais (cadeias de caracteres, números, true, false, null) <br/>
    Exemplos:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  As expressões que usar operadores (= =,! =, > =, >, < =, <) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   As expressões que usar operadores booleanos (& &, | |,!, ^) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   As expressões que usar os operadores numérico (+, -, \*, /, %) <br/>
    Exemplos: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Como a habilidade condicional suporta a avaliação, pode usá-lo em cenários de transformação de menor. Por exemplo, veja [definição de habilidades 4](#transformation-example).

## <a name="skill-inputs"></a>Entradas de habilidades
Entradas diferenciam maiúsculas de minúsculas.

| Entrada   | Descrição |
|-------------|-------------|
| condition   | Esta entrada é um [avaliada campo](#evaluated-fields) que representa a condição para avaliar. Esta condição deve ser avaliado como um valor booleano (*true* ou *falso*).   <br/>  Exemplos: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Esta entrada é um [avaliado o campo](#evaluated-fields) que representa o valor a devolver se a condição é avaliada como *verdadeiro*. Cadeias de caracteres de constantes devem ser devolvidas aspas ("e"). <br/>Valores de exemplo: <br/> "= 'contract'"<br/>"= $(/ documento/contractType)" <br/> "= $(/documento/entidades/\*)" <br/> |
| whenFalse   | Esta entrada é um [avaliado o campo](#evaluated-fields) que representa o valor a devolver se a condição é avaliada como *falso*. <br/>Valores de exemplo: <br/> "= 'contract'"<br/>"= $(/ documento/contractType)" <br/> "= $(/documento/entidades/\*)" <br/>

## <a name="skill-outputs"></a>Saídas de habilidades
Há uma única saída que é simplesmente chamada "output". Devolve o valor *whenFalse* se a condição for falsa ou *whenTrue* se a condição for verdadeira.

## <a name="examples"></a>Exemplos

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Definição de habilidades de exemplo 1: Filtre documentos para devolver apenas os documentos francês

O resultado seguinte devolve uma matriz de frases ("/ documento/frenchSentences") se o idioma do documento for o francês. Se o idioma não for o francês, o valor é definido como *nulo*.

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
Se "/ documento/frenchSentences" é utilizado como o *contexto* de outra habilidade, essa habilidade é executado apenas se "/ documento/frenchSentences" não está definido como *nulo*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Definição de habilidades de exemplo 2: Definir um valor predefinido para um valor que não existe

O resultado seguinte cria uma anotação ("/ documento/languageWithDefault"), que é definida para o idioma do documento ou para "es", se o idioma não está definido.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Definição de habilidades de exemplo 3: Intercalar os valores dos dois campos em um

Neste exemplo, alguns frases tem um *frenchSentiment* propriedade. Sempre que o *frenchSentiment* propriedade é nula, o que queremos usar o *englishSentiment* valor. Podemos atribuir a saída para um membro que é chamado *sentimentos* ("/ documentar/sentimentos / * / sentimento").

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Definição de habilidades de exemplo 4: Transformação de dados num único campo

Neste exemplo, recebemos uma *sentimentos* que está entre 0 e 1. Queremos transformá-los de estar entre -1 e 1. Podemos usar o condicional de habilidades para fazer esta transformação pequenas.

Neste exemplo, não usamos o aspecto condicional da habilidade porque a condição é sempre *true*.

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
Alguns parâmetros são avaliados, por isso terá de se tomar bastante cuidado para seguir o padrão documentado. As expressões tem de começar com um sinal de igual. Um caminho têm de ser delimitado por "$(" and ")". Certifique-se colocar as cadeias de caracteres de plicas. Que ajuda o avaliador de distinguir entre cadeias de caracteres e caminhos concretos e operadores. Além disso, certifique-se de colocar espaços em branco em torno de operadores (por exemplo, um "*" num caminho significa algo diferente de multiplicação).


## <a name="next-steps"></a>Passos Seguintes

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
