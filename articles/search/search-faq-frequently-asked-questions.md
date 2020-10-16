---
title: Perguntas mais frequentes (FAQ)
titleSuffix: Azure Cognitive Search
description: Obtenha respostas a perguntas comuns sobre o serviço microsoft Azure Cognitive Search, um serviço de pesquisa hospedado na nuvem no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 9d6acdcf9487b2d1a5964d4ec686cd23666275b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923097"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure Cognitive Search - perguntas frequentes (FAQ)

 Encontre respostas para perguntas comumente feitas sobre conceitos, código e cenários relacionados com a Pesquisa Cognitiva Azure.

## <a name="platform"></a>Plataforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Como é que a Pesquisa Cognitiva do Azure é diferente da pesquisa completa de texto no meu DBMS?

A Azure Cognitive Search suporta múltiplas fontes de dados, [análise linguística para muitas línguas,](/rest/api/searchservice/language-support) [análise personalizada para entradas de dados interessantes e incomuns, controlos](/rest/api/searchservice/custom-analyzers-in-azure-search)de classificação de pesquisa através de perfis de [pontuação](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), e funcionalidades de experiência de utilizador, como tipahead, destaque de impacto e navegação frontal. Também inclui outras funcionalidades, como sinónimos e sintaxe de consulta rica, mas essas características geralmente não são diferenciadoras.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Posso parar o serviço de Pesquisa Cognitiva do Azure e parar de cobrar?

Não pode parar o serviço. Os recursos computacionais e de armazenamento são atribuídos para uso exclusivo quando o serviço é criado. Não é possível libertar e recuperar esses recursos a pedido.

## <a name="indexing-operations"></a>Operações de Indexação

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Mover, fazer backup e restaurar índices ou instantâneos de índice?

Durante a fase de desenvolvimento, poderá querer mover o seu índice entre os serviços de pesquisa. Por exemplo, pode utilizar um nível de preços básico ou gratuito para desenvolver o seu índice e, em seguida, pretende movê-lo para o standard ou nível superior para uso de produção. 

Ou, pode querer fazer backup de uma imagem de índice para ficheiros que podem ser usados para restaurá-lo mais tarde. 

Pode fazer todas estas coisas com o código de amostra **de backback de índice-back-restore** neste [repo de amostra Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

Também pode [obter uma definição de índice](/rest/api/searchservice/get-index) a qualquer momento usando a API de Pesquisa Cognitiva Azure.

Atualmente, não existe nenhuma característica de extração de índice incorporada, instantâneo ou de restauro de backup no portal Azure. No entanto, estamos a considerar adicionar a funcionalidade de backup e restaurar numa futura versão. Se quiser mostrar o seu suporte a esta funcionalidade, vote no [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Posso restaurar o meu índice ou serviço uma vez que seja apagado?

Não, se eliminar um índice ou serviço de Pesquisa Cognitiva Azure, não poderá ser recuperado. Quando elimina um serviço de Pesquisa Cognitiva Azure, todos os índices do serviço são apagados permanentemente. Se eliminar um grupo de recursos Azure que contenha um ou mais serviços de Pesquisa Cognitiva Azure, todos os serviços são eliminados permanentemente.  

Recriar recursos como índices, indexadores, fontes de dados e skillsets requer que os recrie a partir de código. 

Para recriar um índice, é necessário re indexar dados de fontes externas. Por esta razão, recomenda-se que guarde uma cópia principal ou cópia de segurança dos dados originais noutra loja de dados, como a Base de Dados Azure SQL ou a Cosmos DB.

Como alternativa, pode utilizar o código de amostra **de backup-backup** neste [Azure Cognitive Search .NET sample repo](https://github.com/Azure-Samples/azure-search-dotnet-samples) para fazer backup de uma definição de índice e instantâneo de índice para uma série de ficheiros JSON. Mais tarde, pode utilizar a ferramenta e os ficheiros para restaurar o índice, se necessário.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Posso indexar a partir de réplicas sql database (aplica-se aos [indexantes da base de dados Azure SQL)](./search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Não existem restrições à utilização de réplicas primárias ou secundárias como fonte de dados quando se constrói um índice de raiz. No entanto, refrescar um índice com atualizações incrementais (com base em registos alterados) requer a réplica primária. Este requisito provém da Base de Dados SQL, que garante apenas o rastreio de alterações nas réplicas primárias. Se tentar utilizar réplicas secundárias para uma carga de trabalho de atualização de índices, não há garantia de que obtenha todos os dados.

## <a name="search-operations"></a>Operações de Busca

### <a name="can-i-search-across-multiple-indexes"></a>Posso pesquisar vários índices?

Não, esta operação não é apoiada. A pesquisa é sempre procurada para um único índice.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Posso restringir o acesso do índice de pesquisa pela identidade do utilizador?

Pode implementar [filtros de segurança](./search-security-trimming-for-azure-search.md) com `search.in()` filtro. O filtro compõe-se bem com [serviços de gestão de identidade como o Azure Ative Directory (AAD)](./search-security-trimming-for-azure-search-with-aad.md) para aparar os resultados de pesquisa com base na adesão definida ao grupo de utilizadores.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Por que não há correspondências em termos que sei serem válidos?

O caso mais comum é não saber que cada tipo de consulta suporta diferentes comportamentos de pesquisa e níveis de análises linguísticas. A pesquisa completa de texto, que é a carga de trabalho predominante, inclui uma fase de análise de linguagem que decompõe os termos das formas de raiz. Este aspeto da análise de consulta lança uma rede mais ampla sobre possíveis partidas, porque o termo simbólico corresponde a um maior número de variantes.

As consultas wildcard, fuzzy e regex, no entanto, não são analisadas como consultas regulares de termo ou frases e podem levar a má recordação se a consulta não corresponder à forma analisada da palavra no índice de pesquisa. Para obter mais informações sobre a análise e análise de consultas, consulte [a arquitetura de consulta.](./search-lucene-query-architecture.md)

### <a name="my-wildcard-searches-are-slow"></a>As minhas buscas são lentas.

A maioria das consultas de pesquisa wildcard, como prefixo, fuzzy e regex, são reescritas internamente com termos correspondentes no índice de pesquisa. Este processamento extra de digitalização do índice de pesquisa aumenta a latência. Além disso, consultas de pesquisa amplas, como `a*` por exemplo, que são suscetíveis de ser reescritas com muitos termos podem ser muito lentas. Para pesquisas de wildcard performantes, considere definir um [analisador personalizado.](/rest/api/searchservice/custom-analyzers-in-azure-search)

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Por que a classificação de pesquisa é uma pontuação constante ou igual de 1.0 para cada batida?

Por padrão, os resultados de pesquisa são pontuados com base nas [propriedades estatísticas dos termos correspondentes,](search-lucene-query-architecture.md#stage-4-scoring)e ordenados alto a baixo no conjunto de resultados. No entanto, alguns tipos de consulta (wildcard, prefixo, regex) sempre contribuem com uma pontuação constante para a pontuação geral do documento. Este comportamento é propositado. A Azure Cognitive Search impõe uma pontuação constante para permitir que os jogos encontrados através da expansão de consulta sejam incluídos nos resultados, sem afetar o ranking.

Por exemplo, suponha que uma entrada de "tour*" numa pesquisa wildcard produz fósforos em "tours", "tourettes" e "tourmaline". Dada a natureza destes resultados, não há forma de inferir razoavelmente quais os termos mais valiosos do que outros. Por esta razão, ignoramos as frequências de termos ao marcar resultados em consultas de tipos wildcard, prefixo e regex. Os resultados da pesquisa com base numa entrada parcial recebem uma pontuação constante para evitar distorções em relação a jogos potencialmente inesperados.

## <a name="skillset-operations"></a>Operações Skillset

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>Existem alguma dica ou truques para reduzir os encargos dos serviços cognitivos na ingestão?

É compreensível que não queira executar habilidades incorporadas ou habilidades personalizadas mais do que é absolutamente necessário, especialmente se estiver a lidar com milhões de documentos para processar. Com isso em mente, adicionámos capacidades de "enriquecimento incremental" à execução de skillset. Na sua essência, pode fornecer uma localização de cache (uma cadeia de ligação de armazenamento de bolhas) que será usada para armazenar a saída de etapas de enriquecimento "intermédios".  Isso permite que o gasoduto de enriquecimento seja inteligente e aplique apenas enriquecimentos necessários quando modifica o seu skillset. Isto irá, naturalmente, também poupar tempo de indexação, uma vez que o gasoduto será mais eficiente.

Saiba mais sobre [enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>Padrões de estrutura

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Qual é a melhor abordagem para implementar a pesquisa localizada?

A maioria dos clientes escolhe campos dedicados em vez de uma coleção quando se trata de apoiar diferentes locais (idiomas) no mesmo índice. Os campos específicos do local possibilitam a atribuição de um analisador apropriado. Por exemplo, atribuir o Analisador Francês da Microsoft a um campo que contenha cordas francesas. Também simplifica a filtragem. Se você sabe que uma consulta é iniciada em uma página fr-fr, você pode limitar os resultados da pesquisa a este campo. Ou criar um [perfil de pontuação](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) para dar ao campo mais peso relativo. A Azure Cognitive Search suporta mais de [50 analisadores linguísticos](./search-language-support.md) para escolher.

## <a name="next-steps"></a>Passos seguintes

A sua pergunta é sobre uma funcionalidade ou funcionalidade em falta? Solicite a funcionalidade no site da [Voz do Utilizador.](https://feedback.azure.com/forums/263029-azure-search)

## <a name="see-also"></a>Consulte também

 [StackOverflow: Azure Cognitive Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)  
 [O que é Azure Cognitive Search?](search-what-is-azure-search.md)