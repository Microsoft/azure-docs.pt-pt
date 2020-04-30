---
title: Habilidade de pesquisa cognitiva de pesquisa cognitiva de entidade personalizada
titleSuffix: Azure Cognitive Search
description: Extraide diferentes entidades personalizadas do texto num pipeline de pesquisa cognitiva Azure Cognitive Search. Esta habilidade está atualmente em pré-visualização pública.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 3659070d4ffd4346a8827d2748e67db436fc15b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085744"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Competência cognitiva de procura de entidade personalizada (Pré-visualização)

> [!IMPORTANT] 
> Esta habilidade está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . Atualmente não existe nenhum suporte de Portal ou .NET SDK.

A habilidade de procura de **entidade personalizada** procura texto a partir de uma lista personalizada e definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência fuzzy que pode ser aplicado para encontrar fósforos que são semelhantes, mas não exatamente.  

Esta habilidade não está ligada a uma API de Serviços Cognitivos e pode ser usada gratuitamente durante o período de pré-visualização. No entanto, deve ainda [anexar um recurso dos Serviços Cognitivos](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)para anular o limite de enriquecimento diário. O limite diário aplica-se ao livre acesso aos Serviços Cognitivos quando acedido através da Pesquisa Cognitiva Azure.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Limites de dados
+ O tamanho máximo do recorde de entrada suportado é de 256 MB. Se precisar de separar os seus dados antes de enviá-los para a habilidade de procura de entidades personalizadas, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).
+ A tabela de definição de entidades máximas suportada é de 10 MB se for fornecida utilizando o parâmetro definição de *entidadesDefinitionUri.* 
+ Se as entidades forem definidas inlineline, utilizando o parâmetro *inlineEntidadesDefinição,* o tamanho máximo suportado é de 10 KB.

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| entidadesDefinitionUri    | Caminho para um ficheiro JSON ou CSV contendo todo o texto-alvo para corresponder. Esta definição de entidade é lida no início de uma execução de indexante; quaisquer atualizações a este ficheiro a meio da execução não serão realizadas até que as execuções subsequentes. Este config deve estar acessível em HTTPS. Consulte o formato de definição de [entidade personalizada"](#custom-entity-definition-format) abaixo para obter o esquema csv ou JSON esperado.|
|inlineEntidadesDefinição | Definições de entidade sons a ninline JSON. Este parâmetro substitui as entidadesDefinitionUri parâmetro se estiver presente. Não podem ser fornecidos mais de 10 KB de configuração em linha. Consulte a [Definição](#custom-entity-definition-format) de Entidade Personalizada abaixo para obter o esquema esperado da JSON. |
|código de idioma padrão |    (Opcional) Código linguístico do texto de entrada utilizado para tokenizar e delinear texto de entrada. Apoiam-se as seguintes `da, de, en, es, fi, fr, it, ko, pt`línguas: . O padrão é`en`inglês . Se passar um formato de código de idioma-country, apenas é utilizada a parte do idioma do formato.  |


## <a name="skill-inputs"></a>Inputs de habilidade

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| texto          | O texto para analisar.          |
| languageCode    | Opcional. A predefinição é `"en"`.  |


## <a name="skill-outputs"></a>Saídas de habilidades


| Nome de saída      | Descrição                   |
|---------------|-------------------------------|
| entidades | Uma série de objetos que contêm informações sobre os fósforos que foram encontrados, e metadados relacionados. Cada uma das entidades identificadas pode conter os seguintes campos:  <ul> <li> *nome*: A entidade de alto nível identificada. A entidade representa o formulário "normalizado". </li> <li> *id*: Um identificador único para a entidade definido pelo utilizador no "Formato de Definição de Entidade Personalizada".</li> <li> *descrição*: Descrição da entidade definida pelo utilizador no "Formato de Definição de Entidade Aduaneira". </li> <li> *tipo:* Tipo de entidade definido pelo utilizador no "Formato de Definição de Entidade Personalizada".</li> <li> *subtipo:* Subtipo de entidade definido pelo utilizador no "Formato de Definição de Entidade Personalizada".</li>  <li> *fósforos*: Recolha que descreve cada uma das partidas para essa entidade no texto de origem. Cada partida terá os seguintes membros: </li> <ul> <li> *texto*: O texto bruto corresponde ao documento de origem. </li> <li> *offset*: O local onde a correspondência foi encontrada no texto. </li> <li> *comprimento*: O comprimento do texto combinado. </li> <li> *matchDistance*: O número de caracteres diferentes desta partida era do nome ou pseudónimo original da entidade.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Formato de Definição de Entidade Personalizada

Existem 3 formas diferentes de fornecer a lista de entidades personalizadas à habilidade de Procura de Entidades Personalizadas. Pode fornecer a lista em . Ficheiro CSV, a . Ficheiro JSON ou como uma definição inline como parte da definição de habilidade.  

Se o ficheiro de definição for a . CSV ou . Arquivo JSON, o caminho do ficheiro precisa de ser fornecido como parte do parâmetro *entidadeSDefinitionUri.* Neste caso, o ficheiro é descarregado uma vez no início de cada execução de indexador. O ficheiro deve estar acessível desde que o indexante se destine a ser executado. Além disso, o ficheiro deve ser codificado UTF-8.

Se a definição for fornecida inline, deve ser fornecida tão inline como o conteúdo do parâmetro de habilidade *inlineEntidadesDefinição.* 

### <a name="csv-format"></a>Formato CSV

Pode fornecer a definição das entidades personalizadas a procurar num ficheiro De Valor Separado com ma (CSV), fornecendo o caminho para o ficheiro e definindo-o no parâmetro de *habilidades das entidadesDefinitionUri.* O caminho deve estar em um local https. O ficheiro de definição pode ter até 10 MB de tamanho.

O formato CSV é simples. Cada linha representa uma entidade única, como mostrado abaixo:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

Neste caso, existem três entidades que podem ser devolvidas como entidades encontradas (Bill Gates, Satya Nadella, Microsoft), mas serão identificadas se algum dos termos na linha (pseudónimos) for em conformidade com o texto. Por exemplo, se a corda "William H. Gates" for encontrada num documento, será devolvida uma correspondência para a entidade "Bill Gates".

### <a name="json-format"></a>Formato JSON

Pode fornecer a definição das entidades personalizadas para procurar num ficheiro JSON também. O formato JSON dá-lhe um pouco mais de flexibilidade, uma vez que permite definir regras de correspondência por período. Por exemplo, pode especificar a distância de correspondência difusa (distância Damerau-Levenshtein) para cada período ou se a correspondência deve ser sensível à caixa ou não. 

 Tal como acontece com os ficheiros CSV, é necessário fornecer o caminho para o ficheiro JSON e defini-lo no parâmetro de *habilidades definiçãoUri das entidades.* O caminho deve estar em um local https. O ficheiro de definição pode ter até 10 MB de tamanho.

A definição de lista de entidades personalizadas mais básicas da JSON pode ser uma lista de entidades que correspondam:

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

Um exemplo mais complexo de uma definição JSON pode opcionalmente fornecer o id, descrição, tipo e subtipo de cada entidade - bem como outros *pseudónimos*. Se um termo de pseudónimo for igualado, a entidade será devolvida também:

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

As tabelas abaixo descrevem mais detalhes os diferentes parâmetros de configuração que pode definir ao definir as entidades para combinar:

|  Nome do campo  |        Descrição  |
|--------------|----------------------|
| nome | O descritor de entidade de alto nível. Os fósforos na saída de habilidades serão agrupados por este nome, e deve representar a forma "normalizada" do texto que está a ser encontrado.  |
| descrição  | (Opcional) Este campo pode ser utilizado como uma passagem para metadados personalizados sobre os textos compatíveis. O valor deste campo aparecerá com cada correspondência da sua entidade na saída de habilidades. |
| tipo | (Opcional) Este campo pode ser utilizado como uma passagem para metadados personalizados sobre os textos compatíveis. O valor deste campo aparecerá com cada correspondência da sua entidade na saída de habilidades. |
| subtipo | (Opcional) Este campo pode ser utilizado como uma passagem para metadados personalizados sobre os textos compatíveis. O valor deste campo aparecerá com cada correspondência da sua entidade na saída de habilidades. |
| ID | (Opcional) Este campo pode ser utilizado como uma passagem para metadados personalizados sobre os textos compatíveis. O valor deste campo aparecerá com cada correspondência da sua entidade na saída de habilidades. |
| casoSensível | (Opcional) Incumprimentos a falsos. Valor booleano denotando se as comparações com o nome da entidade devem ser sensíveis ao invólucro de caracteres. Correspondências insensíveis de "Microsoft" podem ser: microsoft, microSoft, MICROSOFT |
| fuzzyEditDistance | (Opcional) Incumprimentos a 0. Valor máximo de 5. Denota o número aceitável de caracteres divergentes que ainda constituem uma correspondência com o nome da entidade. A menor confusão possível para qualquer partida é devolvida.  Por exemplo, se a distância de edição estiver definida para 3, o "Windows 10" ainda corresponderia a "Windows", "Windows10" e "Windows 7". <br/> Quando a sensibilidade do caso é definida como falsa, as diferenças de caso não contam para a tolerância de fuzziness, mas de outra forma contam. |
| padrãoCaseSensitive | (Opcional) Altera o valor de sensibilidade do caso padrão para esta entidade. É usado para alterar o valor padrão de todos os pseudónimos casovalores sensíveis. |
| padrãoFuzzyEditDistance | (Opcional) Altera o valor de distância de edição padrão para esta entidade. Pode ser usado para alterar o valor padrão de todos os pseudónimos valores fuzzyEditDistance. |
| pseudónimos | (Opcional) Uma variedade de objetos complexos que podem ser usados para especificar ortografias alternativas ou sinónimos ao nome da entidade-raiz. |

| Propriedades do pseudónimo | Descrição |
|------------------|-------------|
| texto  | A ortografia alternativa ou representação de algum nome de entidade-alvo.  |
| casoSensível | (Opcional) Atua o mesmo que o parâmetro "caseSensitive" da entidade raiz acima, mas aplica-se apenas a este pseudónimo. |
| fuzzyEditDistance | (Opcional) Atua o mesmo que o parâmetro "fuzzyEditDistance" da entidade raiz acima, mas aplica-se apenas a este pseudónimo. |


### <a name="inline-format"></a>Formato inline

Em alguns casos, pode ser mais conveniente fornecer a lista de entidades personalizadas para combinar diretamente com a definição de habilidade. Nesse caso, pode utilizar um formato JSON semelhante ao acima descrito, mas está inlineado na definição de habilidade.
Apenas configurações com menos de 10 KB de tamanho (tamanho serializado) podem ser definidas em linha. 

##    <a name="sample-definition"></a>Definição de amostra

Uma definição de habilidade de amostra utilizando um formato inline é mostrada abaixo:

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
        "type": "Harware",
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
Alternativamente, se decidir fornecer um ponteiro para o ficheiro de definição de entidades, uma definição de habilidade de amostra utilizando o formato entidadesDefinitionUri é mostrada abaixo:

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

##    <a name="sample-input"></a>Entrada da amostra

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Resultado da amostra

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

## <a name="errors-and-warnings"></a>Erros e advertências

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Aviso: Alcançou a capacidade máxima para os fósforos, saltando todos os outros jogos duplicados.

Este aviso será emitido se o número de fósforos detetados for superior ao máximo permitido. Neste caso, deixaremos de incluir jogos duplicados. Se isso for inaceitável para si, por favor preencha um bilhete de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para que possamos ajudá-lo com o seu caso de uso individual.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Competência de Reconhecimento de Entidades (para procurar entidades conhecidas)](cognitive-search-skill-entity-recognition.md)
