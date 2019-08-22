---
title: Perguntas frequentes (FAQ)-Azure Search
description: Obtenha respostas para perguntas comuns sobre o Microsoft Azure Serviço de Pesquisa, um serviço de pesquisa hospedado na nuvem no Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d4aae2f2ef9ccbc645647125682d999c11c99ab6
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649843"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Azure Search-perguntas frequentes (FAQ)

 Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados a Azure Search.

## <a name="platform"></a>Plataforma

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Como Azure Search diferente da pesquisa de texto completo em meu DBMS?

O Azure Search dá suporte a várias fontes de dados, [análise linguística para muitas linguagens](https://docs.microsoft.com/rest/api/searchservice/language-support), [análise personalizada para entradas de dados interessantes e incomuns](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), pesquisa de controles de classificação por meio de [perfis de Pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)e recursos de experiência do usuário, como typeahead, realce de visita e navegação facetada. Ele também inclui outros recursos, como sinônimos e sintaxe de consulta avançada, mas eles geralmente não diferenciam recursos.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Qual é a diferença entre Azure Search e Elasticsearch?

Ao comparar as tecnologias de pesquisa, os clientes frequentemente pedem informações específicas sobre como Azure Search se compara com Elasticsearch. Os clientes que escolhem Azure Search sobre Elasticsearch para seus projetos de aplicativo de pesquisa normalmente fazem isso porque tornamos uma tarefa fundamental mais fácil ou precisam da integração interna com outras tecnologias da Microsoft:

+ Azure Search é um serviço de nuvem totalmente gerenciado com contratos de nível de serviço (SLA) de 99,9% quando provisionado com redundância suficiente (2 réplicas para acesso de leitura, 3 réplicas para leitura/gravação).
+ Os [processadores de idioma natural](https://docs.microsoft.com/rest/api/searchservice/language-support) da Microsoft oferecem análise linguística de ponta.  
+ [Azure Search indexadores](search-indexer-overview.md) podem rastrear uma variedade de fontes de dados do Azure para indexação inicial e incremental.
+ Se você precisar de resposta rápida a flutuações em volumes de consulta ou indexação, poderá usar [controles deslizantes](search-manage.md#scale-up-or-down) na portal do Azure ou executar um [script do PowerShell](search-manage-powershell.md), ignorando o gerenciamento de fragmentos diretamente.  
+ Os [recursos de Pontuação e ajuste](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) fornecem os meios para influenciar as pontuações de classificação de pesquisa além do que o mecanismo de pesquisa pode fornecer sozinho.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Posso pausar Azure Search serviço e parar a cobrança?

Não é possível pausar o serviço. Os recursos computacionais e de armazenamento são alocados para seu uso exclusivo quando o serviço é criado. Não é possível lançar e recuperar esses recursos sob demanda.

## <a name="indexing-operations"></a>Operações de indexação

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Índices de backup e restauração (ou download e movimentação) ou instantâneos de índice?

Embora você possa [obter uma definição de índice](https://docs.microsoft.com/rest/api/searchservice/get-index) a qualquer momento, não há nenhum recurso de extração de índice, instantâneo ou restauração de backup para baixar um índice *preenchido* em execução na nuvem para um sistema local ou movê-lo para outro serviço de Azure Search.

Os índices são criados e preenchidos a partir do código que você escreve e executados somente em Azure Search na nuvem. Normalmente, os clientes que desejam mover um índice para outro serviço fazem isso editando seu código para usar um novo ponto de extremidade e, em seguida, executar a indexação novamente. Se você quiser a capacidade de tirar um instantâneo ou fazer backup de um índice, converta um voto na [voz do usuário](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Posso restaurar meu índice ou serviço quando ele for excluído?

Não, você não pode restaurar índices ou serviços. Se você excluir um índice de Azure Search, a operação será final e o índice não poderá ser recuperado. Quando você exclui um serviço de Azure Search, todos os índices no serviço são excluídos permanentemente. Além disso, se você excluir um grupo de recursos do Azure que contém um ou mais serviços do Azure Search, todos os serviços serão excluídos permanentemente.  

Restaurar recursos como índices, indexadores, fontes de dados e habilidades exige que você os recrie a partir do código. No caso de índices, você deve reindexar dados de fontes externas. Por esse motivo, é altamente recomendável que você retenha uma cópia mestra ou um backup dos dados originais em outro armazenamento de dados, como o Azure SQL Database ou o Cosmos DB.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Posso indexar de réplicas do banco de dados SQL (aplica-se aos indexadores do [banco de dados SQL do Azure](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Não há restrições quanto ao uso de réplicas primárias ou secundárias como uma fonte de dados ao criar um índice do zero. No entanto, a atualização de um índice com atualizações incrementais (com base em registros alterados) requer a réplica primária. Esse requisito vem do banco de dados SQL, que garante o controle de alterações somente nas réplicas primárias. Se você tentar usar réplicas secundárias para uma carga de trabalho de atualização de índice, não haverá nenhuma garantia de que você obtenha todos os dados.

## <a name="search-operations"></a>Operações de pesquisa

### <a name="can-i-search-across-multiple-indexes"></a>Posso pesquisar em vários índices?

Não, essa operação não tem suporte. A pesquisa sempre está no escopo de um único índice.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Posso restringir o acesso ao índice de pesquisa por identidade do usuário?

Você pode implementar [filtros](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) de segurança `search.in()` com o filtro. O filtro se compõe bem com os [serviços de gerenciamento de identidade como Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) para cortar os resultados da pesquisa com base na associação de grupo de usuários definida.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Por que há zero correspondências em termos que sei ser válidos?

O caso mais comum é não saber que cada tipo de consulta dá suporte a diferentes comportamentos de pesquisa e níveis de análises linguísticas. A pesquisa de texto completo, que é a carga de trabalho predominante, inclui uma fase de análise de linguagem que quebra os termos para os formulários raiz. Esse aspecto da análise de consulta converte uma rede mais ampla sobre possíveis correspondências, pois o termo indexado corresponde a um número maior de variantes.

As consultas curinga, difusa e Regex, no entanto, não são analisadas como consultas de termo ou expressão regulares e podem levar a uma recuperação ruim se a consulta não corresponder à forma analisada da palavra no índice de pesquisa. Para mais informações sobre análise e análise de consulta, consulte [arquitetura de consulta](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Minhas pesquisas de curinga estão lentas.

A maioria das consultas de pesquisa de caractere curinga, como prefixo, difusa e Regex, são reescritas internamente com termos correspondentes no índice de pesquisa. Esse processamento extra de verificação do índice de pesquisa aumenta a latência. Além disso, muitas consultas de pesquisa `a*` amplas, como por exemplo, que provavelmente serão reescritas com muitos termos podem ser muito lentas. Para pesquisas de curingas de desempenho, considere definir um [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Por que a classificação de pesquisa tem uma pontuação constante ou igual a 1,0 para cada clique?

Por padrão, os resultados da pesquisa são pontuados com base nas [propriedades estatísticas de termos correspondentes](search-lucene-query-architecture.md#stage-4-scoring)e ordenados de alto para baixo no conjunto de resultados. No entanto, alguns tipos de consulta (curinga, prefixo, Regex) sempre contribuem com uma pontuação constante para a pontuação geral do documento. Este comportamento é propositado. Azure Search impõe uma pontuação constante para permitir que as correspondências encontradas por meio da expansão de consulta sejam incluídas nos resultados, sem afetar a classificação.

Por exemplo, suponha que uma entrada de "Tour *" em uma pesquisa curinga produza correspondências em "Tours", "turrão" e "passarela". Devido à natureza desses resultados, não há como inferir de forma razoável quais termos são mais valiosos do que outros. Por esse motivo, ignoramos as frequências de termo quando a pontuação resulta em consultas de tipos curinga, prefixo e Regex. Os resultados da pesquisa com base em uma entrada parcial recebem uma pontuação constante para evitar a tendência em relação a correspondências potencialmente inesperadas.

## <a name="design-patterns"></a>Padrões de estrutura

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Qual é a melhor abordagem para implementar a pesquisa localizada?

A maioria dos clientes escolhe campos dedicados em uma coleção quando se trata de dar suporte a localidades diferentes (idiomas) no mesmo índice. Os campos específicos de localidade possibilitam atribuir um analisador apropriado. Por exemplo, atribuir o Microsoft French Analyzer a um campo que contém cadeias de caracteres francesas. Ele também simplifica a filtragem. Se você souber que uma consulta é iniciada em uma página fr-fr, poderá limitar os resultados da pesquisa a esse campo. Ou crie um [perfil de Pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) para dar ao campo mais peso relativo. O Azure Search dá suporte a mais de [50 analisadores de idioma](https://docs.microsoft.com/azure/search/search-language-support) para sua escolha.

## <a name="next-steps"></a>Passos Seguintes

Sua pergunta sobre um recurso ou uma funcionalidade ausente? Solicite o recurso no [site do User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Consulte também

 [StackOverflow Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)  
 [O que é o Azure Search?](search-what-is-azure-search.md)
