---
title: Perguntas Mais Frequentes (FAQ)
titleSuffix: Azure Cognitive Search
description: Obtenha respostas a perguntas comuns sobre o serviço de pesquisa cognitiva Microsoft Azure, um serviço de pesquisa hospedado em nuvem no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d7ba62c795e23e41a1947def77300ffe5d2cc010
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262456"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Pesquisa Cognitiva Azure - perguntas frequentes (FAQ)

 Encontre respostas a perguntas comumente feitas sobre conceitos, códigoe cenários relacionados com a Pesquisa Cognitiva Azure.

## <a name="platform"></a>Plataforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Como é que a Pesquisa Cognitiva Azure é diferente da pesquisa completa de texto no meu DBMS?

A Azure Cognitive Search suporta múltiplas fontes de dados, [análise linguística para muitas línguas,](https://docs.microsoft.com/rest/api/searchservice/language-support) [análise personalizada para inputs de dados interessantes e incomuns,](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)controlos de classificação de pesquisa através de perfis de [pontuação,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)e funcionalidades de experiência de utilizador como typeahead, destaque de sucesso e navegação facetada. Também inclui outras características, como sinónimos e sintaxe de consulta rica, mas geralmente não são características diferenciantes.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Posso parar o serviço de pesquisa cognitiva Azure e parar de faturar?

Não pode parar o serviço. Os recursos computacionais e de armazenamento são atribuídos para o seu uso exclusivo quando o serviço é criado. Não é possível libertar e recuperar esses recursos a pedido.

## <a name="indexing-operations"></a>Operações de Indexação

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Mover, apoiar e restaurar índices ou instantâneos de índice?

Durante a fase de desenvolvimento, pode querer mover o seu índice entre serviços de pesquisa. Por exemplo, pode utilizar um nível de preços Básico ou Livre para desenvolver o seu índice e, em seguida, querer movê-lo para o nível Standard ou superior para uso da produção. 

Ou, pode querer fazer backup de um instantâneo de índice para ficheiros que podem ser usados para restaurá-lo mais tarde. 

Pode fazer todas estas coisas com o código de amostra **de recuperação de cópias** de segurança de índice neste repo de [amostra azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

Também pode [obter uma definição de índice](https://docs.microsoft.com/rest/api/searchservice/get-index) a qualquer momento utilizando a API de PESQUISA Cognitiva Azure.

Atualmente não existe nenhuma característica de extração de índice incorporado, instantâneo ou restauro de backup no portal Azure. No entanto, estamos a considerar adicionar a funcionalidade de backup e restaurar uma futura versão. Se quiser mostrar o seu suporte a esta funcionalidade, vote na [Voz do Utilizador](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Posso restaurar o meu índice ou serviço uma vez que seja apagado?

Não, se eliminar um índice ou serviço de Pesquisa Cognitiva Azure, não pode ser recuperado. Ao eliminar um serviço de Pesquisa Cognitiva Azure, todos os índices do serviço são eliminados permanentemente. Se eliminar um grupo de recursos Azure que contenha um ou mais serviços de Pesquisa Cognitiva Azure, todos os serviços são eliminados permanentemente.  

Recriar recursos como índices, indexadores, fontes de dados e habilidades requer que os recrie a partir do código. 

Para recriar um índice, é necessário reindexar os dados de fontes externas. Por esta razão, recomenda-se que mantenha uma cópia principal ou cópia de segurança dos dados originais noutra loja de dados, como a Base de Dados Azure SQL ou cosmos DB.

Como alternativa, pode utilizar o código de amostra de **restauro de cópias** de segurança de índice seletiva neste representante da [amostra Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) para fazer backup de uma definição de índice e de um instantâneo de índice para uma série de ficheiros JSON. Mais tarde, pode utilizar a ferramenta e os ficheiros para restaurar o índice, se necessário.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Posso indexar a partir de réplicas de bases de dados SQL (Aplica-se aos indexadores de bases de [dados Azure SQL)](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)

Não existem restrições à utilização de réplicas primárias ou secundárias como fonte de dados na construção de um índice de raiz. No entanto, refrescar um índice com atualizações incrementais (com base em registos alterados) requer a réplica primária. Este requisito provém da Base de Dados SQL, que garante o rastreio de alterações apenas nas réplicas primárias. Se tentar utilizar réplicas secundárias para uma carga de trabalho de atualização de índice, não há garantia de obter todos os dados.

## <a name="search-operations"></a>Operações de Pesquisa

### <a name="can-i-search-across-multiple-indexes"></a>Posso pesquisar em vários índices?

Não, esta operação não é apoiada. A procura é sempre remepto a um único índice.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Posso restringir o acesso do índice de pesquisa por identidade do utilizador?

Pode implementar [filtros](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) `search.in()` de segurança com filtro. O filtro compõe-se bem com serviços de gestão de identidade como o [Azure Ative Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) para aparar os resultados de pesquisa com base na adesão definida do grupo de utilizadores.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Por que não há fósforos em termos que sei que são válidos?

O caso mais comum é não saber que cada tipo de consulta suporta diferentes comportamentos de pesquisa e níveis de análises linguísticas. A pesquisa completa de texto, que é a carga de trabalho predominante, inclui uma fase de análise de linguagem que decompõe termos a formas de raiz. Este aspeto da consulta de parsing lança uma rede mais ampla em possíveis partidas, porque o termo tokenizado corresponde a um maior número de variantes.

Wildcard, perguntas difusas e regex, no entanto, não são analisadas como consultas de termo ou frase regulares e podem levar a uma má recordação se a consulta não corresponder à forma analisada da palavra no índice de pesquisa. Para mais informações sobre consulta e análise, consulte a arquitetura de [consulta.](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)

### <a name="my-wildcard-searches-are-slow"></a>As minhas buscas são lentas.

A maioria das consultas de pesquisa wildcard, como prefixo, fuzzy e regex, são reescritas internamente com termos correspondentes no índice de pesquisa. Este processamento extra de digitalização do índice de pesquisa aumenta a latência. Além disso, amplas `a*` consultas de pesquisa, como por exemplo, que são suscetíveis de ser reescritas com muitos termos podem ser muito lentas. Para pesquisas performant wildcard, considere definir um [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Porque é que a pontuação de pesquisa é uma pontuação constante ou igual de 1.0 para cada batida?

Por padrão, os resultados da pesquisa são pontuados com base nas [propriedades estatísticas dos termos correspondentes](search-lucene-query-architecture.md#stage-4-scoring), e ordenados altos a baixos no conjunto de resultados. No entanto, alguns tipos de consultas (wildcard, prefixo, regex) sempre contribuem com uma pontuação constante para a pontuação geral do documento. Este comportamento é propositado. A Azure Cognitive Search impõe uma pontuação constante para permitir que os jogos encontrados através da expansão da consulta sejam incluídos nos resultados, sem afetar o ranking.

Por exemplo, suponha que uma entrada de "tour*" numa pesquisa wildcard produz fósforos em "tours", "tourettes" e "tourmaline". Dada a natureza destes resultados, não há forma de inferir razoavelmente quais os termos mais valiosos do que outros. Por esta razão, ignoramos as frequências de termo quando a pontuação resulta em consultas de tipos wildcard, prefixo e regex. Os resultados da pesquisa com base numa entrada parcial são dados uma pontuação constante para evitar distorções para partidas potencialmente inesperadas.

## <a name="design-patterns"></a>Padrões de estrutura

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Qual é a melhor abordagem para implementar a pesquisa localizada?

A maioria dos clientes escolhe campos dedicados em vez de uma coleção quando se trata de apoiar diferentes locais (idiomas) no mesmo índice. Os campos específicos do local tornam possível atribuir um analisador apropriado. Por exemplo, atribuir o Microsoft French Analyzer a um campo contendo cordas francesas. Também simplifica a filtragem. Se souber que uma consulta é iniciada numa página fr-fr, pode limitar os resultados da pesquisa a este campo. Ou criar um [perfil de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) para dar ao campo mais peso relativo. A Pesquisa Cognitiva Azure suporta mais de [50 analisadores](https://docs.microsoft.com/azure/search/search-language-support) de idiomas para escolher.

## <a name="next-steps"></a>Passos seguintes

A sua pergunta sobre uma funcionalidade ou funcionalidade em falta? Solicite a funcionalidade no site da Voz do [Utilizador](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Consulte também

 [StackOverflow: Pesquisa Cognitiva Azure](https://stackoverflow.com/questions/tagged/azure-search)   
 [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)  
 [O que é a Pesquisa Cognitiva do Azure?](search-what-is-azure-search.md)
