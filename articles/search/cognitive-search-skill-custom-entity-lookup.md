---
title: Competência de pesquisa cognitiva de entidade personalizada
titleSuffix: Azure Cognitive Search
description: Extrair diferentes entidades personalizadas a partir de texto num pipeline de pesquisa cognitiva Azure Cognitive Search. Esta habilidade está atualmente em visualização pública.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 704763e8e6e7c5336d0ed3e1c28791fb96c77aba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97844956"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Competência cognitiva de procura de entidade personalizada (pré-visualização)

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente não existe porta ou suporte .NET SDK.

A habilidade **de Pesquisa de Entidade Personalizada** procura texto a partir de uma lista personalizada, definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência difusa que pode ser aplicada para encontrar fósforos que são semelhantes, mas não exatamente exatos.  

Esta habilidade não está ligada a uma API de Serviços Cognitivos e pode ser utilizada gratuitamente durante o período de pré-visualização. No entanto, deve [ainda anexar um recurso de Serviços Cognitivos](./cognitive-search-attach-cognitive-services.md)para anular o limite diário de enriquecimento. O limite diário aplica-se ao acesso gratuito aos Serviços Cognitivos quando acedido através da Azure Cognitive Search.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.customentityLookupSkill 

## <a name="data-limits"></a>Limites de dados
+ O tamanho máximo do registo de entrada suportado é de 256 MB. Se precisar de separar os seus dados antes de os enviar para a competência de procura da entidade personalizada, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).
+ A tabela de definição de entidades máximas suportada é de 10 MB se for fornecida utilizando o parâmetro *DefinitionUri das entidades.* 
+ Se as entidades forem definidas em linha, utilizando o parâmetro *inlineEntitiesDefinition,* o tamanho máximo suportado é de 10 KB.

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Caminho para um ficheiro JSON ou CSV contendo todo o texto-alvo para combinar. Esta definição de entidade é lida no início de uma execução indexante; quaisquer atualizações a este ficheiro a meio do percurso não serão realizadas até que os subsequentes sejam executados. Este config deve estar acessível em HTTPS. Consulte o formato [de definição de entidade personalizada"](#custom-entity-definition-format) abaixo para o esquema esperado de CSV ou JSON.|
|`inlineEntitiesDefinition` | Definições de entidades JSON inline. Este parâmetro substitui as entidadesDefinitionUri se estiver presente. Não podem ser fornecidos mais de 10 KB de configuração em linha. Consulte a [definição de entidade personalizada](#custom-entity-definition-format) abaixo para o esquema esperado do JSON. |
|`defaultLanguageCode` |    (Opcional) Código linguístico do texto de entrada utilizado para tokenizar e delinear texto de entrada. São apoiadas as seguintes línguas: `da, de, en, es, fi, fr, it, ko, pt` . O padrão é inglês `en` ( ). Se passar num formato de código de identificação de idiomas, apenas é utilizada a parte do código de idiomas do formato.  |
|`globalDefaultCaseSensitive` | (Opcional) Valor sensível a caso padrão para a habilidade. Se `defaultCaseSensitive` o valor de uma entidade não for especificado, este valor passará a ser o valor para essa `defaultCaseSensitive` entidade. |
|`globalDefaultAccentSensitive` | (Opcional) Valor sensível ao sotaque padrão para a habilidade. Se `defaultAccentSensitive` o valor de uma entidade não for especificado, este valor passará a ser o valor para essa `defaultAccentSensitive` entidade. |
|`globalDefaultFuzzyEditDistance` | (Opcional) Valor de distância de edição difusa padrão para a habilidade. Se `defaultFuzzyEditDistance` o valor de uma entidade não for especificado, este valor passará a ser o valor para essa `defaultFuzzyEditDistance` entidade. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Description                   |
|---------------|-------------------------------|
| `text`          | O texto para analisar.          |
| `languageCode`    | Opcional. A predefinição é `"en"`.  |


## <a name="skill-outputs"></a>Saídas de competências


| Nome de saída      | Description                   |
|---------------|-------------------------------|
| `entities` | Uma série de objetos que contêm informações sobre os fósforos que foram encontrados, e metadados relacionados. Cada uma das entidades identificadas pode conter os seguintes campos:  <ul> <li> *nome*: A entidade de alto nível identificada. A entidade representa o formulário "normalizado". </li> <li> *id*: Um identificador único para a entidade tal como definido pelo utilizador no "Formato de Definição de Entidade Personalizada".</li> <li> *descrição*: Descrição da entidade tal como definida pelo utilizador no "Formato de Definição de Entidade Personalizada". </li> <li> *tipo:* Tipo de entidade tal como definido pelo utilizador no "Formato de Definição de Entidade Personalizada".</li> <li> *subtipo:* Subtipo de entidade definido pelo utilizador no "Formato de Definição de Entidade Personalizada".</li>  <li> *fósforos*: Coleção que descreve cada uma das partidas para essa entidade no texto de origem. Cada partida terá os seguintes membros: </li> <ul> <li> *texto*: O texto em bruto corresponde ao documento de origem. </li> <li> *offset*: O local onde a partida foi encontrada no texto. </li> <li> *comprimento*: O comprimento do texto combinado. </li> <li> *matchDistance*: O número de caracteres diferentes esta partida era do nome ou pseudónimo original da entidade.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Formato de definição de entidade personalizada

Existem 3 maneiras diferentes de fornecer a lista de entidades personalizadas para a habilidade de Procuração de Entidade Personalizada. Pode fornecer a lista em um . Ficheiro CSV, a . Ficheiro JSON ou como definição inline como parte da definição de habilidade.  

Se o ficheiro de definição for um . CSV ou . . Ficheiro JSON, o caminho do ficheiro deve ser fornecido como parte do parâmetro *DefinitionUri das entidades.* Neste caso, o ficheiro é descarregado uma vez no início de cada execução do indexante. O ficheiro deve estar acessível enquanto o indexante se destinar a funcionar. Além disso, o ficheiro deve ser codificado UTF-8.

Se a definição for fornecida em linha, deve ser fornecida como o conteúdo do parâmetro de habilidade *inlineEntitiesDefinition.* 

### <a name="csv-format"></a>Formato CSV

Pode fornecer a definição das entidades personalizadas a procurar num ficheiro Comma-Separated Valor (CSV), fornecendo o caminho para o ficheiro e definindo-o no parâmetro de *habilidades DefinitionUri das entidades.* O caminho deve estar em um local https. O ficheiro de definição pode ter até 10 MB de tamanho.

O formato CSV é simples. Cada linha representa uma entidade única, como mostra abaixo:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

Neste caso, existem três entidades que podem ser devolvidas como entidades encontradas (Bill Gates, Satya Nadella, Microsoft), mas serão identificadas se algum dos termos da linha (pseudónimos) for igualado no texto. Por exemplo, se a corda "William H. Gates" for encontrada num documento, será devolvida uma correspondência para a entidade "Bill Gates".

### <a name="json-format"></a>Formato JSON

Você pode fornecer a definição das entidades personalizadas para procurar em um arquivo JSON também. O formato JSON dá-lhe um pouco mais de flexibilidade, uma vez que lhe permite definir regras correspondentes por período. Por exemplo, pode especificar a distância de correspondência difusa (distância Damerau-Levenshtein) para cada termo ou se a correspondência deve ser sensível a caso ou não. 

 Tal como acontece com os ficheiros CSV, é necessário fornecer o caminho para o ficheiro JSON e defini-lo no parâmetro *de habilidades DefinitionUri das entidades.* O caminho deve estar em um local https. O ficheiro de definição pode ter até 10 MB de tamanho.

A definição de lista de entidades personalizadas JSON mais básica pode ser uma lista de entidades a combinar:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Um exemplo mais complexo de uma definição JSON pode opcionalmente fornecer o id, descrição, tipo e subtipo de cada entidade - bem como *outros pseudónimos*. Se um termo pseudónimo for igualado, a entidade também será devolvida:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

As tabelas abaixo descrevem em mais detalhes os diferentes parâmetros de configuração que pode definir ao definir as entidades para combinar:

|  Nome do campo  |        Description  |
|--------------|----------------------|
| `name` | O descritor de entidade de alto nível. Os fósforos na saída de habilidades serão agrupados por este nome, e devem representar a forma "normalizada" do texto encontrado.  |
| `description`  | (Opcional) Este campo pode ser utilizado como um passthrough para metadados personalizados sobre os textos combinados. O valor deste campo aparecerá a cada partida da sua entidade na produção de competências. |
| `type` | (Opcional) Este campo pode ser utilizado como um passthrough para metadados personalizados sobre os textos combinados. O valor deste campo aparecerá a cada partida da sua entidade na produção de competências. |
| `subtype` | (Opcional) Este campo pode ser utilizado como um passthrough para metadados personalizados sobre os textos combinados. O valor deste campo aparecerá a cada partida da sua entidade na produção de competências. |
| `id` | (Opcional) Este campo pode ser utilizado como um passthrough para metadados personalizados sobre os textos combinados. O valor deste campo aparecerá a cada partida da sua entidade na produção de competências. |
| `caseSensitive` | (Opcional) Incumprimentos a falsos. Valor booleano denotando se as comparações com o nome da entidade devem ser sensíveis ao invólucro do carácter. Caso de amostras partidas insensíveis de "Microsoft" podem ser: microsoft, microSoft, MICROSOFT |
| `accentSensitive` | (Opcional) Incumprimentos a falsos. O valor booleano denotando se as letras acentuadas e não acentadas, tais como "é" e "e" devem ser idênticas. |
| `fuzzyEditDistance` | (Opcional) Incumprimentos para 0. Valor máximo de 5. Denota o número aceitável de caracteres divergentes que ainda constituíam uma correspondência com o nome da entidade. A menor confusão possível para qualquer partida é devolvida.  Por exemplo, se a distância de edição for definida para 3, o "Windows 10" ainda corresponderia a "Windows", "Windows10" e "windows 7". <br/> Quando a sensibilidade do caso é definida como falsa, as diferenças de casos NÃO contam para a tolerância à fuzziness, mas de outra forma contam. |
| `defaultCaseSensitive` | (Opcional) Altera o valor de sensibilidade ao caso padrão para esta entidade. Pode ser usado para alterar o valor predefinido de todos os casos de pseudónimos Valoressensíveis. |
| `defaultAccentSensitive` | (Opcional) Altera o valor de sensibilidade ao sotaque padrão para esta entidade. Pode ser usado para alterar o valor padrão de todos os pseudónimos sotaque Valoressensíveis.|
| `defaultFuzzyEditDistance` | (Opcional) Altera o valor de distância de edição padrão para esta entidade. Pode ser usado para alterar o valor padrão de todos os valores de EditDistance. |
| `aliases` | (Opcional) Uma variedade de objetos complexos que podem ser usados para especificar ortografias ou sinónimos alternativos ao nome da entidade raiz. |

| Propriedades de alias | Description |
|------------------|-------------|
| `text`  | A ortografia ou representação alternativa de algum nome de entidade-alvo.  |
| `caseSensitive` | (Opcional) Age o mesmo que o parâmetro "caseSensitive" da entidade raiz acima, mas aplica-se apenas a este pseudónimo. |
| `accentSensitive` | (Opcional) Age o mesmo que o parâmetro "accentSensitive" da entidade raiz acima, mas aplica-se apenas a este pseudónimo. |
| `fuzzyEditDistance` | (Opcional) Age o mesmo que o parâmetro "fuzzyEditDistance" da entidade raiz acima, mas aplica-se apenas a este pseudónimo. |


### <a name="inline-format"></a>Formato inline

Em alguns casos, pode ser mais conveniente fornecer a lista de entidades personalizadas para combinar diretamente com a definição de habilidade. Nesse caso, pode utilizar um formato JSON semelhante ao acima descrito, mas está indicado na definição de habilidade.
Apenas configurações com menos de 10 KB de tamanho (tamanho serializado) podem ser definidas em linha. 

##    <a name="sample-definition"></a>Definição de amostra

Abaixo é apresentada uma definição de habilidade de amostra utilizando um formato inline:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Em alternativa, se decidir fornecer um ponteiro para o ficheiro de definição de entidades, é apresentada abaixo uma definição de habilidade de amostra utilizando o `entitiesDefinitionUri` formato:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Saída de exemplo

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Erros e avisos

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Aviso: Atingiu a capacidade máxima para partidas, saltando todos os jogos duplicados.

Este aviso será emitido se o número de partidas detetadas for superior ao máximo permitido. Neste caso, deixaremos de incluir correspondências duplicadas. Se isto for inaceitável para si, por favor preencha um [bilhete de apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para que possamos ajudá-lo com o seu caso de uso individual.

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Competência de Reconhecimento de Entidades (procurar entidades conhecidas)](cognitive-search-skill-entity-recognition.md)
