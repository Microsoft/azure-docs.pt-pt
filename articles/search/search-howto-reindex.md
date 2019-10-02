---
title: Recriar um índice de Azure Search ou atualizar conteúdo pesquisável-Azure Search
description: Adicione novos elementos, atualize elementos ou documentos existentes ou exclua documentos obsoletos em uma recompilação completa ou indexação incremental parcial para atualizar um índice de Azure Search.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 863050b2646f6f7b3a3d9ba3487f11729bef22c8
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719852"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Como recriar um índice de Azure Search

Este artigo explica como recriar um índice de Azure Search, as circunstâncias sob as quais as recompilações são necessárias e recomendações para mitigar o impacto de recompilações em solicitações de consulta contínuas.

Uma *recompilação* se refere a remover e recriar as estruturas de dados físicos associadas a um índice, incluindo todos os índices invertidos baseados em campo. No Azure Search, não é possível descartar e recriar campos individuais. Para recriar um índice, todo o armazenamento de campo deve ser excluído, recriado com base em um esquema de índice existente ou revisado e, em seguida, repopulado com os dados enviados para o índice ou extraído de fontes externas. É comum recriar índices durante o desenvolvimento, mas talvez você também precise recriar um índice de nível de produção para acomodar alterações estruturais, como adicionar tipos complexos ou adicionar campos a sugestores.

Em contraste com as recompilações que usam um índice offline, a *atualização de dados* é executada como uma tarefa em segundo plano. Você pode adicionar, remover e substituir documentos com interrupção mínima nas cargas de trabalho de consulta, embora as consultas normalmente demorem mais para serem concluídas. Para obter mais informações sobre como atualizar o conteúdo do índice, consulte [Adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Condições de recompilação

| Condição | Descrição |
|-----------|-------------|
| Alterar uma definição de campo | A revisão de um nome de campo, tipo de dados ou [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) específicos (pesquisável, filtrável, classificável, facetable) requer uma recompilação completa. |
| Atribuir um analisador a um campo | Os [analisadores](search-analyzers.md) são definidos em um índice e, em seguida, atribuídos a campos. Você pode adicionar uma nova definição de analisador a qualquer momento, mas só pode *atribuir* um analisador quando o campo é criado. Isso é verdadeiro para as propriedades do **analisador** e do **indexAnalyzer** . A propriedade **searchAnalyzer** é uma exceção (você pode atribuir essa propriedade a um campo existente). |
| Atualizar ou excluir uma definição do analisador em um índice | Não é possível excluir ou alterar uma configuração existente do analisador (Analyzer, criador, filtro de token ou filtro de caracteres) no índice, a menos que você reconstrua o índice inteiro. |
| Adicionar um campo a um Sugestor | Se um campo já existir e você quiser adicioná-lo a uma construção de [Sugestores](index-add-suggesters.md) , você deverá recompilar o índice. |
| Excluir um campo | Para remover fisicamente todos os rastreamentos de um campo, você precisa recompilar o índice. Quando uma recompilação imediata não é prática, você pode modificar o código do aplicativo para desabilitar o acesso ao campo "excluído". Fisicamente, a definição de campo e o conteúdo permanecem no índice até a próxima recompilação, quando você aplica um esquema que omite o campo em questão. |
| Alternar camadas | Se você precisar de mais capacidade, não haverá nenhuma atualização in-loco no portal do Azure. Um novo serviço deve ser criado e os índices devem ser criados a partir do zero no novo serviço. Para ajudar a automatizar esse processo, você pode usar o código de exemplo **index-backup-restore** neste [Azure Search repositório de exemplo .net](https://github.com/Azure-Samples/azure-search-dotnet-samples). Esse aplicativo fará backup do índice em uma série de arquivos JSON e, em seguida, recriará o índice em um serviço de pesquisa que você especificar.|

Qualquer outra modificação pode ser feita sem afetar as estruturas físicas existentes. Especificamente, as seguintes alterações *não* exigem uma recompilação de índice:

+ Adicionar um novo campo
+ Definir o atributo **recuperável** em um campo existente
+ Definir um **searchAnalyzer** em um campo existente
+ Adicionar uma nova definição de analisador em um índice
+ Adicionar, atualizar ou excluir perfis de Pontuação
+ Adicionar, atualizar ou excluir configurações de CORS
+ Adicionar, atualizar ou excluir synonymMaps

Quando você adiciona um novo campo, os documentos indexados existentes recebem um valor nulo para o novo campo. Em uma atualização futura de dados, os valores de dados de origem externos substituem os nulos adicionados por Azure Search. Para obter mais informações sobre como atualizar o conteúdo do índice, consulte [Adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-or-incremental-indexing"></a>Indexação parcial ou incremental

No Azure Search, não é possível controlar a indexação por campo, optando por excluir ou recriar campos específicos. Da mesma forma, não há nenhum mecanismo interno para [indexação de documentos com base em critérios](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Todos os requisitos que você tem para indexação orientada a critérios precisam ser atendidos por meio de código personalizado.

No entanto, o que você pode fazer facilmente é *atualizar documentos* em um índice. Para muitas soluções de pesquisa, os dados de origem externos são voláteis e a sincronização entre os dados de origem e um índice de pesquisa é uma prática comum. No código, chame a operação [Add, Update ou DELETE Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou o [equivalente do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) para atualizar o conteúdo do índice ou para adicionar valores para um novo campo.

## <a name="partial-indexing-with-indexers"></a>Indexação parcial com indexadores

Os [indexadores](search-indexer-overview.md) simplificam a tarefa de atualização de dados. Um indexador só pode indexar uma tabela ou exibição na fonte de dados externa. Para indexar várias tabelas, a abordagem mais simples é criar uma exibição que une tabelas e projetos as colunas que você deseja indexar. 

Ao usar indexadores que rastreiam fontes de dados externas, verifique a coluna "marca d' água alta" nos dados de origem. Se houver, você poderá usá-lo para detecção de alteração incremental, selecionando apenas as linhas que contêm o conteúdo novo ou revisado. Para o [armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), um campo `lastModified` é usado. No [armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` tem a mesma finalidade. Da mesma forma, o [indexador de banco de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) e o [indexador Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) têm campos para sinalizar atualizações de linha. 

Para obter mais informações sobre indexadores, consulte [visão geral do indexador](search-indexer-overview.md) e [Redefinir API REST do indexador](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Como recriar um índice

Planeje recompilações completas e frequentes durante o desenvolvimento ativo, quando os esquemas de índice estiverem em um estado de fluxo. Para aplicativos que já estão em produção, é recomendável criar um novo índice que é executado lado a lado um índice existente para evitar o tempo de inatividade da consulta.

As permissões de leitura/gravação no nível de serviço são necessárias para atualizações de índice. 

Não é possível recriar um índice no Portal. Programaticamente, você pode chamar a [API REST do índice de atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) ou [APIs do .net equivalentes](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) para uma recompilação completa. Uma solicitação de índice de atualização é idêntica para [criar API REST de índice](https://docs.microsoft.com/rest/api/searchservice/create-index), mas tem um contexto diferente.

O fluxo de trabalho a seguir é tendenciosa em direção à API REST, mas aplica-se igualmente ao SDK do .NET.

1. Ao reutilizar um nome de índice, [descarte o índice existente](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Todas as consultas direcionadas a esse índice são imediatamente descartadas. A exclusão de um índice é irreversível, destruindo o armazenamento físico para a coleção Fields e outras construções. Verifique se você está claro sobre as implicações de excluir um índice antes de descartá-lo. 

2. Formular uma solicitação de [índice de atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) com o ponto de extremidade de serviço, a chave de API e uma [chave de administração](https://docs.microsoft.com/azure/search/search-security-api-keys). Uma chave de administração é necessária para operações de gravação.

3. No corpo da solicitação, forneça um esquema de índice com as definições de campo alteradas ou modificadas. O corpo da solicitação contém o esquema de índice, bem como construções para perfis de pontuação, analisadores, sugestores e opções de CORS. Os requisitos de esquema são documentados em [criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Envie uma solicitação de [índice de atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) para recriar a expressão física do índice em Azure Search. 

5. [Carregue o índice com documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) de uma fonte externa.

Quando você cria o índice, o armazenamento físico é alocado para cada campo no esquema de índice, com um índice invertido criado para cada campo pesquisável. Os campos que não são pesquisáveis podem ser usados em filtros ou expressões, mas não têm índices invertidos e não são pesquisáveis de texto completo nem difusa. Em uma recompilação de índice, esses índices invertidos são excluídos e recriados com base no esquema de índice que você fornece.

Quando você carrega o índice, o índice invertido de cada campo é preenchido com todas as palavras exclusivas e com token de cada documento, com um mapa para as IDs de documento correspondentes. Por exemplo, ao indexar um conjunto de dados de hotéis, um índice invertido criado para um campo de cidade pode conter termos para Seattle, Portland e assim por diante. Os documentos que incluem Seattle ou Portland no campo cidade teriam sua ID de documento listada junto com o termo. Em qualquer operação de [adição, atualização ou exclusão](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) , a lista de termos e ID do documento é atualizada de acordo.

> [!NOTE]
> Se você tiver rigorosos requisitos de SLA, poderá considerar o provisionamento de um novo serviço especificamente para esse trabalho, com o desenvolvimento e a indexação que ocorrem em pleno isolamento de um índice de produção. Um serviço separado é executado em seu próprio hardware, eliminando qualquer possibilidade de contenção de recursos. Quando o desenvolvimento for concluído, você deixaria o novo índice em vigor, redirecionando as consultas para o novo ponto de extremidade e índice, ou executaria o código concluído para publicar um índice revisado em seu serviço de Azure Search original. Atualmente, não há nenhum mecanismo para mover um índice pronto para uso para outro serviço.

## <a name="view-updates"></a>Exibir atualizações

Você pode começar a consultar um índice assim que o primeiro documento for carregado. Se você souber a ID de um documento, a [API REST do documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) retornará o documento específico. Para um teste mais amplo, você deve aguardar até que o índice seja totalmente carregado e, em seguida, usar consultas para verificar o contexto que você espera ver.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Indexar conjuntos de dados grandes em escala](search-howto-large-index.md)
+ [Indexação no portal](search-import-data-portal.md)
+ [Indexador de banco de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança em Azure Search](search-security-overview.md)