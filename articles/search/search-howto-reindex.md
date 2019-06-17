---
title: Reconstruir um índice da Azure Search ou de atualização de conteúdo pesquisável - Azure Search
description: Adicionar novos elementos, atualizar elementos existentes ou documentos ou eliminar documentos obsoletos numa recriação completa ou a indexação incremental parcial para atualizar o índice da Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60871153"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Como reconstruir um índice da Azure Search

Este artigo explica como reconstruir um índice da Azure Search, as circunstâncias nas quais as recompilações são necessárias e as recomendações para mitigar o impacto de recompilações em pedidos de consulta em curso.

R *reconstruir* refere-se a remover e recriar as estruturas de dados físicos associadas a um índice, incluindo todos os índices em invertida com base em campo. No Azure Search, não pode remover e recriar campos individuais. Para reconstruir um índice, todo o armazenamento de campo tem de ser eliminado, recriados com base num esquema de índice existente ou revisada e, em seguida, repovoado com os dados enviados por push para o índice ou extraídos de fontes externas. É comum para reconstruir índices durante o desenvolvimento, mas também poderá ter de reconstruir um índice de nível de produção para acomodar alterações estruturais, como adicionar tipos complexos ou adicionar campos ao sugestores.

Em contraste com recompilações colocar offline um índice *atualização de dados* é executado como uma tarefa em segundo plano. Pode adicionar, remover e substituir os documentos com a mínima interrupção para cargas de trabalho de consulta, embora normalmente consultas demoram mais tempo a concluir. Para obter mais informações sobre como atualizar o conteúdo de índice, consulte [adicionar, atualizar ou eliminar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Reconstruir condições

| Condição | Descrição |
|-----------|-------------|
| Alterar uma definição de campo | A revisão de um nome de campo, tipo de dados ou específicos [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) (pesquisável, filtrável, ordenável, facetável) requer uma reconstrução completa. |
| Atribuir um analisador para um campo | [Analisadores](search-analyzers.md) são definidos num índice e, em seguida, são atribuídas a campos. Pode adicionar uma nova definição de analisador para um índice em qualquer altura, mas só é possível *atribuir* um analisador quando o campo é criado. Isso é verdadeiro para ambos os **analisador** e **indexAnalyzer** propriedades. O **searchAnalyzer** propriedade é uma exceção (pode atribuir esta propriedade para um campo existente). |
| Atualizar ou eliminar uma definição de analisador num índice | Não é possível eliminar ou alterar uma configuração existente de analyzer (analisador, tokenizer, filtro de token ou filtro de char) no índice, a menos que reconstrua o índice de inteiro. |
| Adicionar um campo para um sugestor | Se já existe um campo e pretende adicioná-lo para um [Sugestores](index-add-suggesters.md) construir, tem de reconstruir o índice. |
| Eliminar um campo | Para remover fisicamente todos os rastreios de um campo, terá de recriar o índice. Quando uma reconstrução imediata não for possível, é possível modificar o código da aplicação para desativar o acesso ao campo "eliminado". Fisicamente, a definição de campo e o conteúdo permanece no índice até a recompilação de seguinte, quando aplicar um esquema que omite o campo em questão. |
| Escalões de comutador | Se necessitar de mais capacidade, não existe nenhuma atualização no local. É criado um novo serviço no ponto de nova capacidade e índices devem ser criados a partir do zero no novo serviço. |

Qualquer modificação de outra pode ser feita sem afetar as estruturas físicas existentes. Especificamente, as seguintes alterações fazer *não* exigem uma recompilação de índice:

+ Adicionar um novo campo
+ Definir o **recuperável** atributo num campo existente
+ Definir um **searchAnalyzer** num campo existente
+ Adicionar uma nova definição de analisador num índice
+ Adicionar, atualizar ou eliminar perfis de classificação
+ Adicionar, atualizar ou eliminar definições de CORS
+ Adicionar, atualizar ou eliminar synonymMaps

Quando adiciona um novo campo, documentos indexados existentes tem um valor nulo para o novo campo. Numa atualização de dados futuros, valores dos dados de origem externa substituem os valores nulos adicionados pelo Azure Search. Para obter mais informações sobre como atualizar o conteúdo de índice, consulte [adicionar, atualizar ou eliminar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-or-incremental-indexing"></a>Parcial ou incrementais de indexação

No Azure Search, não é possível controlar indexação numa base por campo, optando por eliminar ou recrie a campos específicos. Da mesma forma, não existe nenhum mecanismo incorporado para [indexar documentos com base nos critérios](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Quaisquer requisitos que tiver para indexação baseadas em critérios precisam ser atendidos por meio de código personalizado.

No entanto, o que pode fazer facilmente, é *atualizar documentos* num índice. Para várias soluções de pesquisa, dados de origem externa são volátil e sincronização entre os dados de origem e um índice de pesquisa é uma prática comum. No código, chamar o [adicionar, atualizar ou eliminar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operação ou o [equivalente de .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) para atualizar o conteúdo de índice, ou para adicionar valores para um novo campo.

## <a name="partial-indexing-with-indexers"></a>Parcial de indexação com indexadores

[Indexadores](search-indexer-overview.md) simplificar a tarefa de atualização de dados. Um indexador pode indexar apenas uma tabela ou vista da origem de dados externa. Para várias tabelas de índice, a abordagem mais simples é criar uma vista que associa tabelas e projetos as colunas que deseja indexar. 

Quando utilizar indexadores que pesquisam origens de dados externas, verifique a existência de uma coluna de "máximo" nos dados de origem. Se existir, pode utilizá-lo para a deteção de alteração de incremental, escolhendo apenas essas linhas que contêm conteúdo novo ou revisado. Para [armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), um `lastModified` campo é utilizado. No [armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` tem a mesma finalidade. Da mesma forma, ambos [indexador do Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) e [indexador do Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) ter campos para sinalizando atualizações de linha. 

Para obter mais informações sobre indexadores, consulte [descrição geral do indexador](search-indexer-overview.md) e [repor o indexador REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Como reconstruir um índice

Tencione recompilações freqüentes e completas durante o desenvolvimento do Active Directory, quando os esquemas de índice estão num estado recém-implantados. Para aplicativos já em produção, recomendamos que crie um novo índice que é executado lado a lado de um índice existente para evitar o tempo de inatividade de consulta.

Permissões de leitura / escrita no nível de serviço são necessárias para atualizações de índices. 

Não é possível reconstruir um índice no portal. Por meio de programação, pode chamar [API de REST do índice de atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) ou [APIs equivalentes do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) para uma reconstrução completa. Um pedido de índice de atualização é idêntico à [criar API REST do índice](https://docs.microsoft.com/rest/api/searchservice/create-index), mas tem um contexto diferente.

O seguinte fluxo de trabalho é viés da API REST, mas aplica-se igualmente ao SDK do .NET.

1. Quando reutilizar um nome de índice [remova o índice existente](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Quaisquer consultas de direcionamento desse índice imediatamente são ignoradas. Eliminar um índice é irreversível, destruição de armazenamento físico para a coleção de campos e outras construções. Certifique-se de que está claro sobre as implicações de eliminar um índice antes de removê-lo. 

2. Formular uma [índice de atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) pedido com o seu ponto final de serviço, chave de API e um [chave de administrador](https://docs.microsoft.com/azure/search/search-security-api-keys). Uma chave de administração é necessária para operações de escrita.

3. No corpo do pedido, forneça um esquema de índice com as definições de campo alterados ou modificados. O corpo do pedido contém o esquema de índice, bem como constrói para a classificação de perfis, analisadores, sugestores e opções CORS. Requisitos de esquema estão documentados em [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Enviar um [índice de atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) pedido para reconstruir a expressão física do índice na Azure Search. 

5. [Carregar o índice com documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) de uma origem externa.

Ao criar o índice, o armazenamento físico é alocado para cada campo no esquema do índice, com um índice invertido criado para cada campo pesquisável. Campos que não podem ser pesquisados podem ser utilizados em filtros ou expressões, mas não têm invertido índices e estão não texto completo ou difusa pesquisáveis. Numa recompilação de índice, esses índices invertidas são eliminados e recriados com base no esquema de índice que fornecer.

Quando carrega o índice, o índice de invertida de cada campo é preenchido com todas as palavras exclusivas, com token de cada documento, com um mapa para IDs de documento correspondente. Por exemplo, quando um conjunto de dados de hotéis a indexação, um índice invertido criado para um campo de cidade pode conter termos para Seattle, Portland e assim por diante. Documentos que incluem Seattle ou Portland no campo Cidade teria o ID de documento listado juntamente com o termo. Em qualquer [adicionar, atualizar ou eliminar](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operação, os termos e a lista de ID do documento são atualizados em conformidade.

> [!NOTE]
> Se tiver rigorosos requisitos de SLA, considere um novo serviço especificamente para esse trabalho, com o desenvolvimento de aprovisionamento e a indexação que ocorrem no isolamento completo de um índice de produção. Um serviço separado é executado no seu próprio hardware, eliminando qualquer possibilidade de contenção de recursos. Quando o desenvolvimento é concluído, a optar por deixaria o novo índice em vigor, redirecionando consultas para o novo ponto final e o índice ou, deverá executar o código concluído para publicar um índice revisado no seu serviço de Azure Search original. Atualmente, não existe nenhum mecanismo para mover um índice de prontos a utilizar para o outro serviço.

## <a name="view-updates"></a>Ver atualizações

Pode começar a consultar um índice, assim que o primeiro documento é carregado. Se souber o ID de um documento, o [API de REST do documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) devolve o documento em particular. Para testes mais abrangente, deve aguardar até que o índice está totalmente carregado e, em seguida, utilizar as consultas para verificar o contexto que é esperar.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Índice grandes conjuntos de dados em escala](search-howto-large-index.md)
+ [Indexação no portal](search-import-data-portal.md)
+ [Indexador da base de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança no Azure Search](search-security-overview.md)