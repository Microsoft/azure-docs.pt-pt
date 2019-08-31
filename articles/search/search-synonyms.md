---
title: Sinônimos para expansão de consulta em um índice de pesquisa-Azure Search
description: Crie um mapa de sinônimos para expandir o escopo de uma consulta de pesquisa em um índice de Azure Search. O escopo é ampliado para incluir termos equivalentes que você fornecer em uma lista.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
manager: nitinme
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d9ddb5af42c538558a69ce68e7ea90161c947b12
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186454"
---
# <a name="synonyms-in-azure-search"></a>Sinônimos no Azure Search

Os sinônimos nos mecanismos de pesquisa associam termos equivalentes que expandem implicitamente o escopo de uma consulta, sem que o usuário tenha que realmente fornecer o termo. Por exemplo, considerando o termo "cachorro" e as associações de sinônimo de "canino" e "filhote", todos os documentos contendo "cachorro", "canino" ou "filhote" ficarão dentro do escopo da consulta.

No Azure Search, a expansão do sinônimo é feita no momento da consulta. Você pode adicionar mapas de sinônimos a um serviço sem interrupções nas operações existentes. Você pode adicionar uma propriedade **synonymMaps** a uma definição de campo sem precisar recriar o índice.

## <a name="create-synonyms"></a>Criar sinônimos

Não há suporte do portal para criar sinônimos, mas você pode usar a API REST ou o SDK do .NET. Para começar com o REST, é recomendável [usar o postmaster](search-get-started-postman.md) e a formulação de solicitações usando esta API: [Crie mapas](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)de sinônimos. Para C# os desenvolvedores, você pode começar a [Adicionar sinônimos na pesquisa do Azure C#usando ](search-synonyms-tutorial-sdk.md)o.

Opcionalmente, se você estiver usando [chaves gerenciadas pelo cliente](search-security-manage-encryption-keys.md) para criptografia do lado do serviço em repouso, poderá aplicar essa proteção ao conteúdo do seu mapa de sinônimos.

## <a name="use-synonyms"></a>Utilizar sinónimos

No Azure Search, o suporte a sinônimos é baseado em mapas de sinônimos que você define e carrega em seu serviço. Esses mapas constituem um recurso independente (como índices ou fontes de dados) e podem ser usados por qualquer campo pesquisável em qualquer índice em seu serviço de pesquisa.

Mapas e índices de sinônimos são mantidos de forma independente. Depois de definir um mapa de sinônimos e carregá-lo em seu serviço, você pode habilitar o recurso de sinônimo em um campo adicionando uma nova propriedade chamada **synonymMaps** na definição de campo. Criar, atualizar e excluir um mapa de sinônimos sempre é uma operação de documento inteiro, o que significa que você não pode criar, atualizar ou excluir partes do mapa de sinônimos de forma incremental. Atualizar até mesmo uma única entrada requer um recarregamento.

A incorporação de sinônimos em seu aplicativo de pesquisa é um processo de duas etapas:

1.  Adicione um mapa de sinônimos ao serviço de pesquisa por meio das APIs a seguir.  

2.  Configure um campo pesquisável para usar o mapa de sinônimos na definição do índice.

### <a name="synonymmaps-resource-apis"></a>APIs de recurso SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adicione ou atualize um mapa de sinônimos em seu serviço, usando POST ou PUT.

Os mapas de sinônimos são carregados no serviço por meio de POST ou PUT. Cada regra deve ser delimitada pelo caractere de nova linha (' \n '). Você pode definir até 5.000 regras por mapa de sinônimos em um serviço gratuito e regras de 10.000 em todas as outras SKUs. Cada regra pode ter até 20 expansões.

Mapas de sinônimos devem estar no formato Apache Solr, que é explicado abaixo. Se você tiver um dicionário de sinônimos existente em um formato diferente e quiser usá-lo diretamente, informe-nos [](https://feedback.azure.com/forums/263029-azure-search)no UserVoice.

Você pode criar um novo mapa de sinônimos usando HTTP POST, como no exemplo a seguir:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Como alternativa, você pode usar PUT e especificar o nome do mapa de sinônimos no URI. Se o mapa de sinônimos não existir, ele será criado.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formato de sinônimo do Apache Solr

O formato Solr dá suporte a mapeamentos de sinônimos equivalentes e explícitos. As regras de mapeamento aderem à especificação de filtro de sinônimos de software livre do Apache Solr, descritas neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Abaixo está um exemplo de regra para sinônimos equivalentes.
```
USA, United States, United States of America
```

Com a regra acima, uma consulta de pesquisa "EUA" se expandirá para "EUA" ou "Estados Unidos" ou "Estados Unidos da América".

O mapeamento explícito é indicado por uma seta "= >". Quando especificado, uma sequência de termo de uma consulta de pesquisa que corresponde ao lado esquerdo de "= >" será substituída pelas alternativas no lado direito. Dada a regra abaixo, pesquise as consultas "Washington", "Eu desbota". ou "WA" todos serão reescritos para "WA". O mapeamento explícito só se aplica na direção especificada e não reescreve a consulta "WA" para "Washington" nesse caso.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Liste os mapas de sinônimos em seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Obtenha um mapa de sinônimos em seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Exclua um mapa de sinônimos em seu serviço.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configure um campo pesquisável para usar o mapa de sinônimos na definição do índice.

Uma nova propriedade de campo **synonymMaps** pode ser usada para especificar um mapa de sinônimos a ser usado para um campo pesquisável. Os mapas de sinônimos são recursos de nível de serviço e podem ser referenciados por qualquer campo de um índice no serviço.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** pode ser especificado para campos pesquisáveis do tipo ' EDM. String ' ou ' Collection (EDM. String) '.

> [!NOTE]
> Você só pode ter um mapa de sinônimos por campo. Se você quiser usar vários mapas de sinônimos, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impacto de sinônimos em outros recursos de pesquisa

O recurso de sinônimos regrava a consulta original com sinônimos com o operador OR. Por esse motivo, o realce de pressionamento e os perfis de Pontuação tratam o termo original e os sinônimos como equivalentes.

O recurso de sinônimo se aplica a consultas de pesquisa e não se aplica a filtros ou facetas. Da mesma forma, as sugestões são baseadas apenas no termo original; as correspondências de sinônimo não aparecem na resposta.

As expansões de sinônimo não se aplicam aos termos de pesquisa curinga; os termos de prefixo, difuso e Regex não são expandidos.

Se você precisar fazer uma única consulta que aplica a expansão de sinônimo e curinga, Regex ou pesquisas difusas, você pode combinar as consultas usando a sintaxe ou. Por exemplo, para combinar sinônimos com curingas para sintaxe de consulta simples, o termo seria `<query> | <query>*`.

## <a name="tips-for-building-a-synonym-map"></a>Dicas para criar um mapa de sinônimos

- Um mapa de sinônimos conciso e bem projetado é mais eficiente do que uma lista completa de possíveis correspondências. Dicionários excessivamente grandes ou complexos demoram mais para analisar e afetar a latência de consulta se a consulta se expandir para muitos sinônimos. Em vez de adivinhar em quais termos podem ser usados, você pode obter os termos reais por meio de um [relatório de análise de tráfego de pesquisa](search-traffic-analytics.md).

- Como um exercício preliminar e de validação, habilite e use esse relatório para determinar precisamente quais termos se beneficiarão de uma correspondência de sinônimo e, em seguida, continue a usá-lo como validação de que seu mapa de sinônimos está produzindo um resultado melhor. No relatório predefinido, os blocos "consultas de pesquisa mais comuns" e "consultas de pesquisa de resultado zero" fornecerão as informações necessárias.

- Você pode criar vários mapas de sinônimos para seu aplicativo de pesquisa (por exemplo, por idioma, se seu aplicativo oferecer suporte a uma base de clientes multilíngüe). Atualmente, um campo só pode usar um deles. Você pode atualizar a propriedade synonymMaps de um campo a qualquer momento.

## <a name="next-steps"></a>Passos Seguintes

- Se você tiver um índice existente em um ambiente de desenvolvimento (não de produção), experimente um pequeno dicionário para ver como a adição de sinônimos altera a experiência de pesquisa, incluindo impacto nos perfis de pontuação, realce de visita e sugestões.

- [Habilite a análise de tráfego de pesquisa](search-traffic-analytics.md) e use o relatório de Power bi predefinido para saber quais termos são usados com mais freqüência e quais retornam zero documentos. Munido dessas informações, revise o dicionário para incluir sinônimos de consultas não contratadas que devem ser resolvidas para documentos no índice.
