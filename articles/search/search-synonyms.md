---
title: Sinónimos para expansão de consulta sobre um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Crie um mapa de sinónimo para expandir o âmbito de uma consulta de pesquisa num índice de Pesquisa Cognitiva Azure. O âmbito é alargado para incluir termos equivalentes que fornece numa lista.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251629"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Sinónimos em Pesquisa Cognitiva Azure

Com mapas de sinónimo, pode associar termos equivalentes, expandindo o âmbito de uma consulta sem que o utilizador tenha de fornecer o termo. Por exemplo, assumindo que "cão", "canino" e "cachorrinho" são sinónimos, uma consulta sobre "canino" corresponderá a um documento que contenha "cão".

## <a name="create-synonyms"></a>Criar sinónimos

Um mapa de sinónimo é um ativo que pode ser criado uma vez e usado por muitos índices. O [nível de serviço](search-limits-quotas-capacity.md#synonym-limits) determina quantos mapas de sinónimo pode criar, variando de três mapas de sinónimo para níveis gratuitos e básicos, até 20 para os níveis Standard. 

Pode criar vários mapas de sinónimos para diferentes línguas, como versões em inglês e francês, ou léxicos se o seu conteúdo incluir terminologia técnica ou obscura. Embora possa criar vários mapas de sinónimo no seu serviço de pesquisa, um campo só pode usar um deles.

Um mapa de sinónimo consiste em nome, formato e regras que funcionam como entradas de mapa sinónimo. O único formato suportado é `solr` , e o formato determina a `solr` construção de regras.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

Para criar um mapa de sinónimo, utilize o [Mapa de Synonym (REST API)](/rest/api/searchservice/create-synonym-map) ou um Azure SDK. Para os desenvolvedores C# recomendamos começar com [Add Synonyms in Azure Cognitive Search usando C#](search-synonyms-tutorial-sdk.md).

## <a name="define-rules"></a>Definir regras

As regras de mapeamento aderem à especificação de filtro de sinónimo de código aberto da Apache Solr, descrita neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). O `solr` formato suporta dois tipos de regras:

+ equivalência (em que os termos são substitutos iguais na consulta)

+ mapeamentos explícitos (em que os termos são mapeados para um termo explícito antes da consulta)

Cada regra deve ser delimitada pelo carácter da nova linha ( `\n` ). Você pode definir até 5.000 regras por mapa de sinónimo em um serviço gratuito e 20.000 regras por mapa em outros níveis. Cada regra pode ter até 20 expansões (ou itens de uma regra). Para mais informações, consulte [os limites do Synonym.](search-limits-quotas-capacity.md#synonym-limits)

Os parsers de consulta vão diminuir os termos de caso superior ou misto, mas se quiser preservar caracteres especiais na corda, como uma vírgula ou traço, adicione os caracteres de fuga apropriados ao criar o mapa do sinónimo.

### <a name="equivalency-rules"></a>Regras de equivalência

As regras para termos equivalentes são delimitadas pela vírgula dentro da mesma regra. No primeiro exemplo, uma consulta sobre `USA` irá expandir-se para `USA` OR `"United States"` `"United States of America"` . Note que se quiser coincidir com uma frase, a consulta em si deve ser uma consulta de frases incluídas naspas.

No caso da equivalência, uma consulta para `dog` expandirá a consulta para incluir também `puppy` e `canine` .

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Mapeamento explícito

As regras para um mapeamento explícito são denotadas por uma seta `=>` . Quando especificado, uma sequência de termo de uma consulta de pesquisa que corresponda ao lado esquerdo de `=>` será substituída pelas alternativas no lado direito na hora da consulta.

No caso explícito, uma consulta para `Washington` , `Wash.` ou será `WA` reescrito como `WA` , e o motor de consulta só procurará fósforos no termo `WA` . O mapeamento explícito só se aplica na direção especificada e não reescreve a consulta `WA` `Washington` neste caso.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Escapando de personagens especiais

Os sinónimos são analisados durante o processamento de consultas. Se precisar de definir sinónimos que contenham vírgulas ou outros caracteres especiais, pode escapar-lhes com uma pestana, como neste exemplo:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Uma vez que o backslash é em si um personagem especial em outras línguas como JSON e C#, você provavelmente precisará de escapar duas vezes. Por exemplo, o JSON enviado à API REST para o mapa de sinónimo acima seria assim:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Carregar e gerir mapas de sinónimos

Como mencionado anteriormente, pode criar ou atualizar um mapa de sinónimo sem perturbar a consulta e indexar cargas de trabalho. Um mapa de sinónimo é um objeto autónomo (como índices ou fontes de dados), e enquanto nenhum campo estiver a usá-lo, as atualizações não farão com que a indexação ou as consultas falhem. No entanto, uma vez adicionado um mapa de sinónimo a uma definição de campo, se eliminar um mapa de sinónimo, qualquer consulta que inclua os campos em questão falhará com um erro de 404.

Criar, atualizar e eliminar um mapa de sinónimo é sempre uma operação de documento completo, o que significa que não é possível atualizar ou apagar gradualmente partes do mapa de sinónimos. Atualizar mesmo uma única regra requer uma recarga.

## <a name="assign-synonyms-to-fields"></a>Atribuir sinónimos aos campos

Depois de carregar um mapa de sinónimos, pode ativar os sinónimos em campos do tipo `Edm.String` `Collection(Edm.String)` ou, em campos com `"searchable":true` . Como notado, uma definição de campo pode usar apenas um mapa de sinónimo.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Consulta sobre campos equivalentes ou mapeados

A adição de sinónimos não impõe novos requisitos para a construção de consultas. Pode emitir consultas de termo e frases, tal como fez antes da adição de sinónimos. A única diferença é que se um termo de consulta existe no mapa do sinónimo, o motor de consulta irá expandir ou reescrever o termo ou frase, dependendo da regra.

## <a name="how-synonyms-are-used-during-query-execution"></a>Como os sinónimos são usados durante a execução de consultas

Os sinónimos são uma técnica de expansão de consulta que complementa o conteúdo de um índice com termos equivalentes, mas apenas para campos que têm uma atribuição de sinónimo. Se uma consulta de campo *excluir* um campo ativado por sinónimo, não verá correspondências do mapa do sinónimo.

Para campos com sinónimos, os sinónimos estão sujeitos à mesma análise de texto que o campo associado. Por exemplo, se um campo for analisado usando o analisador padrão Lucene, os termos do sinónimo também estarão sujeitos ao analisador padrão lucene no momento da consulta. Se quiser preservar a pontuação, tais como períodos ou traços, no termo sinónimo, aplique um analisador de preservação de conteúdos no campo.

Internamente, a função de sinónimos reescreve a consulta original com sinónimos com o operador DE. Por esta razão, os perfis de destaque e pontuação de impacto tratam o termo original e os sinónimos como equivalentes.

Os sinónimos aplicam-se apenas a consultas de texto de formulário gratuito e não são suportados para filtros, facetas, autocompletos ou sugestões. O autocompleto e as sugestões baseiam-se apenas no termo original; os jogos de sinónimo não aparecem na resposta.

As expansões de synonym não se aplicam aos termos de pesquisa wildcard; prefixo, fuzzy e termos regex não são expandidos.

Se precisar de fazer uma única consulta que aplique expansão de sinónimo e pesquisas de wildcard, regex ou fuzzy, pode combinar as consultas usando a sintaxe OR. Por exemplo, para combinar sinónimos com wildcards para sintaxe de consulta simples, o termo seria `<query> | <query>*` .

Se tiver um índice existente num ambiente de desenvolvimento (não-produção), experimente com um pequeno dicionário para ver como a adição de sinónimos altera a experiência de pesquisa, incluindo o impacto nos perfis de pontuação, destaque de sucesso e sugestões.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um mapa de sinónimo (REST API)](/rest/api/searchservice/create-synonym-map)