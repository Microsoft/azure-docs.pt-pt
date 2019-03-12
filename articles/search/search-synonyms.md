---
title: Sinónimos para a expansão de consulta através de um índice de pesquisa - Azure Search
description: Crie um mapa de sinónimos para expandir o âmbito de uma consulta de pesquisa num índice da Azure Search. Âmbito é ampliado para incluir termos equivalentes que fornecer numa lista.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
manager: jlembicz
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4383cc327d8058ca44acd892f41a7a256e3b1727
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570441"
---
# <a name="synonyms-in-azure-search"></a>Sinónimos no Azure Search

Sinónimos em mecanismos de pesquisa associam termos equivalentes que implicitamente expandir o âmbito de uma consulta, sem que o utilizador ter de fornecer, na verdade, o termo. Por exemplo, tendo em conta os termo "cachorro" e o sinónimo associações "canine" e "lançamento será feito", todos os documentos que contenham "cachorro", "canine" ou "lançamento será feito" será estão dentro do escopo da consulta.

No Azure Search, expansão de sinónimos é feita no momento da consulta. Pode adicionar mapas de sinónimos a um serviço com nenhuma interrupção para operações existentes. Pode adicionar um **synonymMaps** propriedade a uma definição de campo sem ter de recriar o índice.

## <a name="feature-availability"></a>Funcionalidades disponíveis

A funcionalidade de sinónimos é suportada na versão da api mais recente (api-version = 2017-11-11). Não existe suporte do portal do Azure neste momento.

## <a name="how-to-use-synonyms-in-azure-search"></a>Como utilizar sinónimos no Azure search

No Azure Search, suporte de sinónimos baseia-se em mapas de sinónimos que definem e carregar para o seu serviço. Esses mapas constituem um recurso independente (como índices ou origens de dados) e podem ser utilizados por qualquer campo pesquisável qualquer índice no seu serviço de pesquisa.

Mapas de sinónimos e índices são mantidos de forma independente. Depois de definir um mapa de sinónimos e carregá-lo ao seu serviço, pode ativar a funcionalidade de sinónimos num campo, adicionando uma nova propriedade denominada **synonymMaps** na definição de campo. Criar, atualizar e excluir um mapa de sinónimos sempre são uma operação de documento inteiro, que significa que não é possível criar, atualizar ou excluir partes do mapa de sinónimos de forma incremental. A atualizar até mesmo uma única entrada requer uma recarga.

Incorporar sinónimos na sua aplicação de pesquisa é um processo de dois passos:

1.  Adicione um mapa de sinónimos ao seu serviço de pesquisa através das APIs abaixo.  

2.  Configure um campo pesquisável para utilizar o mapa de sinónimos na definição do índice.

### <a name="synonymmaps-resource-apis"></a>APIs de recurso de SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adicionar ou atualizar um mapa de sinónimos no seu serviço, utilizando publicar ou colocar.

Mapas de sinónimos são carregados para o serviço por meio de POST ou PUT. Cada regra deve ser delimitada por nova linha ("\n"). Pode definir um máximo de 5000 regras por mapa de sinónimos num serviço gratuito e 10 000 regras em todos os outros SKUs. Cada regra pode ter até 20 expansões.

Mapas de sinónimos tem de estar no formato do Apache Solr, que é explicado abaixo. Se tiver um dicionário de sinónimos existente num formato diferente e pretender utilizá-lo diretamente, faça contato no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Pode criar um novo mapa de sinónimos utilizando o HTTP POST, como no exemplo seguinte:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Em alternativa, pode utilizar PUT e especifique o nome do mapa de sinónimos no URI. Se o mapa de sinónimos não existir, será criada.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formato de sinónimos do Apache Solr

O formato de Solr suporta mapeamentos de sinónimos equivalentes e explícito. Regras de mapeamento de respeitar a especificação de filtro de sinónimos de código-fonte aberto de Apache Solr, descritos neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Segue-se um exemplo de regra para sinónimos equivalentes.
```
USA, United States, United States of America
```

Com a regra acima, uma consulta de pesquisa "USA" irá expandir "USA" ou "United States" ou "Estados Unidos da América".

Mapeamento explícito está em falta por uma seta "= >". Quando especificado, uma seqüência de prazo de uma consulta de pesquisa que corresponde ao lado esquerdo do "= >" será substituído por alternativas no lado direito. Tendo em conta a regra abaixo, consultas de pesquisa "Washington", "Washington" ou "WA" serão todos ser reescrito para "WA". Mapeamento explícito apenas aplica-se na direção especificada e não volte a escrever a consulta "WA" para "Washington" em vez disso.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Mapeia sinónimo de lista no seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Obtenha um mapa de sinónimos no seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Elimine um mapa de sinónimos no seu serviço.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configure um campo pesquisável para utilizar o mapa de sinónimos na definição do índice.

Uma nova propriedade de campo **synonymMaps** pode ser utilizado para especificar um mapa de sinónimos a utilizar para um campo pesquisável. Mapas de sinónimos são recursos de nível de serviço e podem ser referenciados por qualquer campo de um índice no serviço.

    POST https://[servicename].search.windows.net/indexes?api-version=2017-11-11
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

**synonymMaps** pode ser especificado para os campos pesquisáveis do tipo 'EDM' ou 'Collection(Edm.String)'.

> [!NOTE]
> Só pode ter um sinónimo mapear por campo. Se pretender utilizar vários mapas de sinónimos, faça contato no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impacto de sinónimos em outros recursos de pesquisa

A funcionalidade de sinónimos reescreve a consulta original com sinónimos com o operador OR. Por esse motivo, o detetor de realce e perfis de classificação tratam o termo e sinónimos original como equivalentes.

Funcionalidade de sinónimos aplica-se a consultas de pesquisa e não se aplica a filtros ou facetas. Da mesma forma, sugestões baseiam-se apenas o termo original; correspondências de sinónimos não aparecem na resposta.

As expansões de sinónimos não são aplicáveis a termos de pesquisa de caráter universal; prefixo, difusa e os termos de regex não são expandidos.

Se precisar de fazer uma única consulta, que se aplica a expansão de sinónimos e universais, regex ou pesquisas difusas, pode combinar as consultas usando a sintaxe de OR. Por exemplo, para combinar sinónimos com carateres universais para a sintaxe de consulta simples, o termo seria `<query> | <query>*`.

## <a name="tips-for-building-a-synonym-map"></a>Sugestões para criar um mapa de sinónimos

- Um mapa de sinónimos concisa, bem projetado é mais eficiente do que uma lista completa das correspondências possíveis. Dicionários excessivamente grandes ou complexos demoram mais tempo a analisar e afetam a latência de consulta, se a consulta se expande para muitos sinónimos. Em vez de adivinhar a que podem ser utilizados termos, pode obter os termos reais por meio de um [relatório de análise de tráfego de pesquisa](search-traffic-analytics.md).

- Como exercitem um preliminar e validação, ativar e, em seguida, utilize este relatório para determinar exatamente quais termos irá beneficiar uma correspondência de sinónimos e, em seguida, continuar a utilizá-lo como validar que o seu mapa de sinónimos é produzir um resultado melhor. No relatório predefinido, os mosaicos "mais comuns consultas de pesquisa" e "consultas de pesquisa de resultado de Zero" fornece as informações necessárias.

- Pode criar vários mapas de sinónimos para a sua aplicação de pesquisa (por exemplo, ao idioma se seu aplicativo oferecer suporte a uma base de clientes em vários idiomas). Atualmente, um campo só pode utilizar um deles. É possível atualizar a propriedade de synonymMaps de um campo em qualquer altura.

## <a name="next-steps"></a>Passos Seguintes

- Se tiver um índice existente num ambiente de desenvolvimento (não produção), experimente um dicionário pequeno para ver como a adição de sinónimos altera a experiência de pesquisa, incluindo o impacto sobre perfis de classificação, detetor e sugestões.

- [Ativar a análise de tráfego de pesquisa](search-traffic-analytics.md) e utilizar o relatório do Power BI predefinido para saber quais termos são utilizados mais e quais devolvem zero documentos. Armado com essas idéias, revise o dicionário para incluir os sinónimos para não produtivo utilizando consultas, que devem resolver para documentos no seu índice.
