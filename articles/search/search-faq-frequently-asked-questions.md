---
title: Perguntas mais frequentes (FAQ) - Azure Search
description: Obtenha respostas para perguntas comuns sobre o serviço de pesquisa do Microsoft Azure, um serviço de pesquisa alojado na cloud no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c77f26187914b2c6e52426bb2a07303b22ccb2b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023985"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>O Azure Search - perguntas mais frequentes sobre (FAQ)

 Encontre respostas para perguntas freqüentes sobre conceitos, código e cenários relacionados com o Azure Search.

## <a name="platform"></a>Plataforma

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Como é o Azure Search diferente da pesquisa em texto completo no meu DBMS?

O Azure Search oferece suporte a várias origens de dados [análise linguística para muitas linguagens](https://docs.microsoft.com/rest/api/searchservice/language-support), [analysis personalizado para entradas de dados interessantes e invulgar](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), procurar controlos de classificação por meio de [de classificação perfis](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)e funcionalidades de experiência do usuário como typeahead, detetor de ocorrências e navegação por facetas. Também inclui outras funcionalidades, como sinónimos e sintaxe de consulta avançada, mas esses são geralmente não diferenciar recursos.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>O que é a diferença entre o Azure Search e o Elasticsearch?

Ao comparar as tecnologias de pesquisa, os clientes costumam perguntar para obter informações específicas sobre como o Azure Search se compara com o Elasticsearch. Clientes que optam por pesquisa do Azure ao longo do Elasticsearch para pesquisa em seus projetos de aplicativos normalmente fazer isso, porque agora fácil uma tarefa principal ou que precisam a integração incorporada com outras tecnologias da Microsoft:

+ O Azure Search é um serviço cloud totalmente gerido com contratos de nível de serviço (SLA) 99,9% quando aprovisionado com redundância suficiente (2 réplicas para acesso de leitura, 3 réplicas para leitura e escrita).
+ Da Microsoft [processadores de linguagem Natural](https://docs.microsoft.com/rest/api/searchservice/language-support) oferecem a análise linguística de vanguarda.  
+ [Indexadores do Azure Search](search-indexer-overview.md) pode rastrear uma variedade de origens de dados do Azure para indexação inicial e incremental.
+ Se precisar de rápida resposta a flutuações de consulta ou indexação de volumes, pode usar [controles slider](search-manage.md#scale-up-or-down) do Azure no portal ou a execução um [script do PowerShell](search-manage-powershell.md), ignorando a gestão de partições horizontais diretamente.  
+ [Classificação e funcionalidades de otimização](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) fornecer os meios para influenciar as pontuações de classificação para além do que pode fornecer o mecanismo de pesquisa apenas de pesquisa.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Pode colocar em pausa do serviço Azure Search e parar a faturação?

Não é possível colocar em pausa o serviço. Recursos computacionais e de armazenamento são alocados para seu uso exclusivo quando o serviço é criado. Não é possível liberar e recuperar esses recursos sob demanda.

## <a name="indexing-operations"></a>Operações de indexação

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Índices de cópia de segurança e restauro (ou download e movimentação) ou instantâneos de índice?

Embora possa [obter uma definição de índice](https://docs.microsoft.com/rest/api/searchservice/get-index) a qualquer momento, não existe a extração de índice, instantâneo ou funcionalidade de restauro da cópia de segurança para baixar um *preenchido* índice em execução na cloud para um sistema local, ou movê-lo para outro serviço de Azure Search.

Índices são criados e preenchidos a partir do código que escreve e ser executado apenas no Azure Search na cloud. Normalmente, os clientes que desejam migrar de um índice para outro serviço fazê-lo ao editar o respetivo código para utilizar um novo ponto final e, em seguida, volte a executar a indexação. Se pretender que a capacidade de tirar um instantâneo ou um índice de cópia de segurança, converter um voto [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Posso restaurar a minha índice ou o serviço assim que for eliminado?

Não, não é possível restaurar índices ou serviços. Se eliminar um índice da Azure Search, a operação é definitiva e não é possível recuperar o índice. Quando elimina um serviço Azure Search, todos os índices no serviço são eliminados permanentemente. Além disso, se eliminar um grupo de recursos do Azure que contém um ou mais serviços do Azure Search, todos os serviços serão eliminados permanentemente.  

A restauração de recursos, tais como índices, indexadores, origens de dados e conjuntos de competências exige recriá-las a partir do código. No caso de índices, tem de reindexar dados a partir de origens externas. Por esse motivo, é altamente recomendável que mantém uma cópia principal ou a cópia de segurança dos dados originais em outro armazenamento de dados, como a base de dados do Azure SQL ou Cosmos DB.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Pode indexar de réplicas de base de dados SQL (aplica-se ao [indexadores de base de dados do Azure SQL](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Não há restrições sobre a utilização de réplicas primárias ou secundárias como uma origem de dados ao criar um índice do zero. No entanto, fazendo a atualização de um índice com atualizações incrementais (com base nos registos alterados) requer que a réplica primária. Este requisito é proveniente de base de dados SQL, garantias de que o registo em réplicas primárias apenas de alterações. Se tentar usar o réplicas secundárias para uma carga de trabalho de atualização de índice, não há nenhuma garantia, que obtém todos os dados.

## <a name="search-operations"></a>Operações de pesquisa

### <a name="can-i-search-across-multiple-indexes"></a>Pode pesquisar em vários índices?

Não, esta operação não é suportada. Pesquisa sempre tem um âmbito para um único índice.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Posso restringir o acesso de índice de pesquisa pela identidade de utilizador?

Pode implementar [filtros de segurança](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) com `search.in()` filtro. O filtro compõe bem com [Directory(AAD) Active Directory do Azure, como de serviços de gestão de identidade](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) cortar os resultados da pesquisa com base em definido associação de grupo do utilizador.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Por que existem zero corresponde à medida que eu sei que sejam válidas?

O caso mais comum é não saber que cada tipo de consulta oferece suporte a comportamentos de pesquisa diferentes e níveis de análises linguísticos. Pesquisa em texto completo, que é a carga de trabalho predominante, inclui uma fase de análise de linguagem que venha termos para baixo para formulários de raiz. Esse aspecto da análise de consulta converte uma rede mais ampla através de correspondências possíveis, uma vez que o termo tokenized corresponde a um maior número de variantes.

Caráter universal, difusa e consultas de regex, no entanto, não são analisadas como consultas de termo ou a expressão regulares e podem levar a solicitação de recolhimento fraco se a consulta não coincide com o formulário analisado da palavra no índice de pesquisa. Para obter mais informações sobre a análise de consulta e análise, consulte [consultar arquitetura](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Meu pesquisas com carateres universais são lentas.

A maioria das consultas de pesquisa de caráter universal, como prefixo, difusa e regex, foram reescritas internamente com correspondência de termos no índice de pesquisa. Este processamento extra de analisar o índice de pesquisa adiciona a latência. Consulta de pesquisa além disso, amplo, como `a*` por exemplo, que são provavelmente seja reescrito com estes termos de muitos pode ser muito lenta. Para pesquisas com carateres universais de alto desempenho, considere definir uma [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Por que é a classificação de pesquisa uma pontuação igual a constante ou igual a 1.0 para todos os acessos?

Por predefinição, os resultados da pesquisa são classificados com base no [propriedades de estatísticas de correspondência de termos](search-lucene-query-architecture.md#stage-4-scoring)e ordenadas o conjunto de cima para baixo no resultado. No entanto, alguns tipos (com carateres universais, prefixo, regex) de consulta sempre contribuir com uma pontuação constante para a classificação de documento geral. Este comportamento é propositado. O Azure Search impõe uma pontuação constante para permitir que foram encontradas correspondências por meio da expansão de consulta a serem incluídos nos resultados, sem afetar a classificação.

Por exemplo, suponha que uma entrada de "apresentação *" numa pesquisa de curinga produz correspondências em "tours", "tourettes" e "tourmaline". Não é dada a natureza desses resultados, é possível inferir razoavelmente os termos encontram-se mais valiosos do que outras pessoas. Por esse motivo, estamos a ignorar freqüências quando os resultados de classificação em consultas de caráter universal de tipos, o prefixo e o regex. Os resultados da pesquisa com base numa entrada parcial recebem uma pontuação constante para evitar ajustes em relação aos correspondências potencialmente inesperadas.

## <a name="design-patterns"></a>Padrões de estrutura

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>O que é a melhor abordagem para a implementação de pesquisas localizada?

A maioria dos clientes escolher campos dedicados ao longo de uma coleção quando se trata de oferecer suporte a localidades diferentes (idiomas) no mesmo índice. Campos específicos da localidade tornam possível atribuir um analisador apropriado. Por exemplo, atribuindo o analisador de francês da Microsoft para um campo que contém as cadeias de caracteres francês. Também simplifica a filtragem. Se sabe de que uma consulta é iniciada numa página de fr-fr, pode limitar os resultados da pesquisa para este campo. Em alternativa, crie uma [perfil de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) para dar o campo de peso relativo mais. O Azure Search oferece suporte ao longo [50 analisadores de idiomas](https://docs.microsoft.com/azure/search/search-language-support) à sua escolha.

## <a name="next-steps"></a>Passos Seguintes

É sua pergunta sobre um recurso ou uma funcionalidade em falta? Pedir a funcionalidade no [web site do Uservoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Consulte também

 [StackOverflow: Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Completa como funciona a pesquisa de texto no Azure Search](search-lucene-query-architecture.md)  
 [O que é o Azure Search?](search-what-is-azure-search.md)
