---
title: Reconstruir um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Adicione novos elementos, atualize os elementos ou documentos existentes ou elimine documentos obsoletos numa reconstrução completa ou indexação parcial para atualizar um índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 8cebe02ebc638ba62fceec80dff2c6724ccf92c8
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212305"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Como reconstruir um índice em Pesquisa Cognitiva Azure

Este artigo explica como reconstruir um índice de Pesquisa Cognitiva Azure, as circunstâncias em que são necessárias reconstruções, e recomendações para mitigar o impacto das reconstruções nos pedidos de consulta em curso.

Uma *reconstrução* refere-se à queda e recriação das estruturas de dados físicos associadas a um índice, incluindo todos os índices invertidos baseados no campo. Na Pesquisa Cognitiva Azure, não é possível largar e recriar campos individuais. Para reconstruir um índice, todo o armazenamento de campo deve ser eliminado, recriado com base num esquema de índice existente ou revisto, e depois repovoado com dados empurrados para o índice ou retirados de fontes externas. 

É comum reconstruir índices durante o desenvolvimento, mas também pode ser necessário reconstruir um índice de nível de produção para acomodar mudanças estruturais, tais como adicionar tipos complexos ou adicionar campos aos sugestionantes.

## <a name="rebuild-conditions"></a>Reconstruir condições

Deixe cair e recrie um índice se alguma das seguintes condições for verdadeira. 

| Condição | Descrição |
|-----------|-------------|
| Alterar uma definição de campo | A revisão de um nome de campo, tipo de dados ou [atributos](https://docs.microsoft.com/rest/api/searchservice/create-index) específicos do índice (pesquisável, filterável, classificativa, facetable) requer uma reconstrução completa. |
| Atribuir um analisador a um campo | [Os analisadores](search-analyzers.md) são definidos num índice e depois atribuídos aos campos. Pode adicionar uma nova definição de analisador a um índice a qualquer momento, mas só pode *atribuir* um analisador quando o campo é criado. Isto é verdade tanto para as propriedades **do analisador** como para o **indexAnalyzer.** A propriedade **searchAnalyzer** é uma exceção (você pode atribuir esta propriedade a um campo existente). |
| Atualizar ou eliminar uma definição de analisador num índice | Não é possível excluir ou alterar uma configuração de analisador existente (analisador, tokenizer, filtro de fichas ou filtro de carvão) no índice, a menos que reconstrua todo o índice. |
| Adicione um campo a um sugestionista | Se um campo já existe e quiser adicioná-lo a uma construção de [Sugestionantes,](index-add-suggesters.md) deve reconstruir o índice. |
| Apagar um campo | Para remover fisicamente todos os vestígios de um campo, tem que reconstruir o índice. Quando uma reconstrução imediata não é prática, pode modificar o código de aplicação para desativar o acesso ao campo "eliminado". Fisicamente, a definição de campo e o conteúdo permanecem no índice até à próxima reconstrução, quando se aplica um esquema que omite o campo em questão. |
| Mudar de nível | Se necessitar de mais capacidade, não existe uma atualização no portal Azure. É necessário criar um novo serviço e basear-se em índices de raiz no novo serviço. Para ajudar a automatizar este processo, pode utilizar o código de amostra **de restauro de cópias** de segurança de índice neste repo de [amostra azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Esta aplicação irá fazer o seu índice de volta para uma série de ficheiros JSON e, em seguida, recriar o índice num serviço de pesquisa que especifica.|

## <a name="update-conditions"></a>Atualizar condições

Muitas outras modificações podem ser feitas sem afetar as estruturas físicas existentes. Especificamente, as seguintes alterações *não* requerem uma reconstrução do índice. Para estas alterações, pode [atualizar uma definição](https://docs.microsoft.com/rest/api/searchservice/update-index) de índice com as suas alterações.

+ Adicione um novo campo
+ Desloque o atributo **recuperável** num campo existente
+ Desloque um **analisador** de pesquisa num campo existente
+ Adicione uma nova definição de analisador num índice
+ Adicionar, atualizar ou eliminar perfis de pontuação
+ Adicionar, atualizar ou excluir as definições cors
+ Adicionar, atualizar ou eliminar sinonymMaps

Quando se adiciona um novo campo, os documentos indexados existentes recebem um valor nulo para o novo campo. Numa futura atualização de dados, os valores provenientes de dados de origem externa substituem os nulos adicionados pela Azure Cognitive Search. Para obter mais informações sobre a atualização do conteúdo do índice, consulte [Adicionar, Atualizar ou Eliminar Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Como reconstruir um índice

Durante o desenvolvimento, o esquema do índice muda frequentemente. Pode planear através da criação de índices que podem ser eliminados, recriados e recarregados rapidamente com um pequeno conjunto de dados representativo. 

Para aplicações já em produção, recomendamos a criação de um novo índice que corre lado a lado um índice existente para evitar o tempo de paragem. O seu código de aplicação fornece reorientação para o novo índice.

1. Determine se é necessária uma reconstrução. Se estiver apenas a adicionar campos, ou a alterar parte do índice que não está relacionado com os campos, poderá simplesmente [atualizar a definição](https://docs.microsoft.com/rest/api/searchservice/update-index) sem apagar, recriar e recarregá-la totalmente.

1. [Obtenha uma definição](https://docs.microsoft.com/rest/api/searchservice/get-index) de índice no caso de precisar para referência futura.

1. [Deixe cair o índice existente,](https://docs.microsoft.com/rest/api/searchservice/delete-index)assumindo que não está a correr índices novos e antigos lado a lado. 

   Quaisquer consultas que apontem para o índice são imediatamente retiradas. Lembre-se que apagar um índice é irreversível, destruindo o armazenamento físico para a coleção de campos e outras construções. Faça uma pausa para pensar nas implicações antes de a largar. 

1. [Criar um índice revisto,](https://docs.microsoft.com/rest/api/searchservice/create-index)onde o corpo do pedido inclua definições de campo alteradas ou modificadas.

1. [Carregue o índice com documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) de uma fonte externa.

Quando se cria o índice, o armazenamento físico é atribuído para cada campo no esquema de índice, com um índice invertido criado para cada campo pesquisável. Os campos que não são pesquisáveis podem ser utilizados em filtros ou expressões, mas não têm índices invertidos e não são pesquisáveis em texto completo ou pesquisáveis. Numa reconstrução do índice, estes índices invertidos são eliminados e recriados com base no esquema de índice que fornece.

Quando carrega o índice, o índice invertido de cada campo é povoado com todas as palavras únicas e tokenizadas de cada documento, com um mapa para identificações de documentocorrespondentes. Por exemplo, ao indexar um conjunto de dados de hotéis, um índice invertido criado para um campo da cidade pode conter termos para Seattle, Portland, e assim por diante. Documentos que incluam Seattle ou Portland no campo da cidade teriam o seu documento de identificação listado juntamente com o termo. Em qualquer operação [de Adicionar, Atualizar ou Eliminar,](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) os termos e a lista de id do documento são atualizados em conformidade.

> [!NOTE]
> Se tiver requisitos sla rigorosos, poderá considerar o fornecimento de um novo serviço especificamente para este trabalho, com o desenvolvimento e a indexação a ocorrerem em total isolamento a partir de um índice de produção. Um serviço separado funciona no seu próprio hardware, eliminando qualquer possibilidade de contenção de recursos. Quando o desenvolvimento estiver concluído, ou deixaria o novo índice no lugar, redirecionando as consultas para o novo ponto final e índice, ou executaria código final para publicar um índice revisto no seu serviço original de Pesquisa Cognitiva Azure. Atualmente, não existe um mecanismo para mover um índice de pronto-a-uso para outro serviço.

## <a name="check-for-updates"></a>Verifique se há novidades

Pode começar a consultar um índice assim que o primeiro documento estiver carregado. Se você sabe o ID de um documento, o [Documento De Procura ÇÃO REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) devolve o documento específico. Para testes mais amplos, deve esperar até que o índice esteja completamente carregado e, em seguida, use consultas para verificar o contexto que espera ver.

## <a name="see-also"></a>Veja também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Indexar grandes conjuntos de dados em escala](search-howto-large-index.md)
+ [Indexação no portal](search-import-data-portal.md)
+ [Indexador de base de dados Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança na Pesquisa Cognitiva Azure](search-security-overview.md)