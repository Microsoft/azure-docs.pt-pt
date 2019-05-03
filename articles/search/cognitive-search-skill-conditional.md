---
title: Habilidade de pesquisa cognitiva condicional (Azure Search) | Documentos da Microsoft
description: Condicional habilidade que permite a filtragem, a criação de padrões e a mesclagem de valores.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028429"
---
#   <a name="conditional-skill"></a>Habilidade condicional

O **habilidade condicional** permite uma variedade de cenários que exigem uma operação booleana decidir os dados que devem ser atribuídos a um resultado. Estes cenários incluem: filtragem, atribuindo um valor predefinido e unir dados com base numa condição.

O pseudocódigo seguinte explica o que faz a habilidade de condicional:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Essa habilidade não está vinculada a uma API dos serviços cognitivos e não lhe é cobrados usá-lo. Deve ainda [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md), no entanto, para substituir o **gratuito** opção de recursos que limita a um pequeno número de diário possível por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campos avaliados

Essa habilidade é especial porque suas entradas estiverem campos avaliados.

Seguem-se os valores válidos de uma expressão:

-   Caminhos de anotação (caminhos em expressões têm de ser delimitados por "$(" and ")") <br/>
    Exemplos:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literais (cadeias de caracteres, números, true, false, null) <br/>
    Exemplos:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  As expressões que usar um operador de comparação (= =,! =, > =, >, < =, <) <br/>
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

-   As expressões que usar um operador numérico (+, -, \*, /, %) <br/>
    Exemplos: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Devido a avaliação suportada, a habilidade de condicional pode ser utilizada para cenários de transformação de pequenas. Veja o exemplo [definição de habilidades 4](#transformation-examples) para obter um exemplo.

## <a name="skill-inputs"></a>Entradas de habilidades
Entradas diferenciam maiúsculas de minúsculas.

| Entradas      | Descrição |
|-------------|-------------|
| condition   | Esta entrada é um [avaliada campo](#evaluated-fields) que representa a condição para avaliar. Esta condição deve avaliar um valor booleano (true ou false).   <br/>  Exemplos: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Esta entrada é um [avaliada campo](#evaluated-fields). O valor a devolver se a condição é avaliada como true. Cadeias de caracteres de constantes devem ser retornadas em ' ' aspas. <br/>Valores de exemplo: <br/> "= 'contract'"<br/>"= $(/ documento/contractType)" <br/> "= $(/documento/entidades/\*)" <br/> |
| whenFalse   | Esta entrada é um [avaliada campo](#evaluated-fields). O valor a devolver se a condição é avaliada como false.  <br/>Valores de exemplo: <br/> "= 'contract'"<br/>"= $(/ documento/contractType)" <br/> "= $(/documento/entidades/\*)" <br/>

## <a name="skill-outputs"></a>Saídas de habilidades
Há uma única saída chamada "output". Ela retornará o valor de whenFalse se a condição for falsa ou whenTrue se a condição for verdadeira.

## <a name="examples"></a>Exemplos

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Definição de habilidades de exemplo 1: Filtragem de documentos para devolver apenas os documentos "Francês"

O resultado seguinte irá devolver uma matriz de frases ("/ documento/frenchSentences"), se o idioma do documento é francês. Se o idioma não for o francês, esse valor será definido como nulo.

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
Se "/ documento/frenchSentences" é utilizado como o *contexto* de outra habilidade, essa habilidade só será executado se "/ documento/frenchSentences" não está definido como nulo


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Definição de habilidades de exemplo 2: Definir um valor predefinido quando não existe.

O resultado seguinte irá criar uma anotação ("/ documento/languageWithDefault"), que é definida como a linguagem do documento, ou "es" se o idioma não está definido.

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

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Definição de habilidades de exemplo 3: Intercalar os valores dos dois campos diferentes num único campo

Neste exemplo, alguns frases tem um *frenchSentiment* propriedade. Sempre que o *frenchSentiment* propriedade é nula, gostamos de usar o *englishSentiment* valor. Podemos atribuir a saída para um membro chamado simplesmente *sentimentos* ("/ documentar/sentimentos / * / sentimento").

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

## <a name="transformation-examples"></a>Exemplos de transformação
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Definição de habilidades de exemplo 4: Execução de transformações de dados num único campo

Neste exemplo, recebemos um sentimento entre 0 e 1 e gostaríamos de transformá-los para que fique entre -1 e 1. Esta é uma transformação de matemática pequeno que podemos fazer usando a habilidade de condicional.

Neste exemplo específico, nunca usamos o aspecto condicional da habilidade como a condição é sempre verdadeira. 

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
Tenha em atenção que alguns dos parâmetros são avaliados, por isso terá de se tomar bastante cuidado seguindo o padrão documentado. As expressões tem de começar com um é igual a início de sessão "=" e caminhos têm de ser delimitados por "$(" and ")". Certifique-se de colocar suas cadeias de caracteres de aspas"" à medida que o vão ajudar o avaliador de distinguir entre cadeias de caracteres e caminhos concretos e operadores. Além disso, certifique-se de colocar um espaço em branco em torno de operadores (por exemplo um * num caminho tem um significado diferente do que o operador de multiplicação).


## <a name="next-steps"></a>Passos Seguintes

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
