---
title: Reconstruir um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Adicione novos elementos, atualize elementos ou documentos existentes ou elimine documentos obsoletos numa reconstrução total ou indexação parcial para refrescar um índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 47e9b80bb25b7ff14695cc67682265fe338ff76f
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119106"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Como reconstruir um índice na Pesquisa Cognitiva Azure

Este artigo explica como reconstruir um índice de Pesquisa Cognitiva Azure, as circunstâncias em que são necessárias reconstruções, e recomendações para mitigar o impacto das reconstruções nos pedidos de consulta em curso.

Uma *reconstrução* refere-se à queda e recriando as estruturas de dados físicos associadas a um índice, incluindo todos os índices invertidos baseados no campo. Na Pesquisa Cognitiva Azure, não é possível largar e recriar campos individuais. Para reconstruir um índice, todo o armazenamento de campo deve ser eliminado, recriado com base num esquema de índice existente ou revisto, e depois repovoado com dados empurrados para o índice ou retirados de fontes externas. 

É comum reconstruir índices durante o desenvolvimento quando você está em iterando sobre o design de índice, mas você também pode precisar reconstruir um índice de nível de produção para acomodar mudanças estruturais, como adicionar tipos complexos ou adicionar campos a sugestionantes.

## <a name="rebuild-versus-refresh"></a>"Reconstruir" versus "refrescar"

A reconstrução não deve ser confundida com o refrescante conteúdo de um índice com documentos novos, modificados ou eliminados. Refrescar um corpus de pesquisa é quase um dado em cada app de pesquisa, com alguns cenários que requerem atualizações atualizadas (por exemplo, quando um corpus de pesquisa precisa refletir as mudanças de inventário numa aplicação de vendas online).

Desde que não mude a estrutura do índice, pode atualizar um índice utilizando as mesmas técnicas que usou para carregar o índice inicialmente:

* Para indexar o modo de pressão, ligue [para adicionar, atualizar ou eliminar documentos](/rest/api/searchservice/addupdate-or-delete-documents) para empurrar as alterações para um índice.

* Para indexadores, pode [agendar a execução do indexante](search-howto-schedule-indexers.md) e utilizar cartões de rastreio ou de tempo para identificar o delta. Se as atualizações tão rapidamente forem refletidas do que o que um programador pode gerir, pode utilizar a indexação do modo de pressão.

## <a name="rebuild-conditions"></a>Reconstruir condições

Largue e recrie um índice se alguma das seguintes condições for verdadeira. 

| Condição | Descrição |
|-----------|-------------|
| Alterar uma definição de campo | A revisão de um nome de campo, tipo de dados ou [atributos](/rest/api/searchservice/create-index) de índice específicos (pescáveis, filtrais, ordenados, facetable) requer uma reconstrução completa. |
| Atribua um analisador a um campo | [Os analisadores](search-analyzers.md) são definidos num índice e depois atribuídos aos campos. Pode adicionar uma nova definição de analisador a um índice a qualquer momento, mas só pode *atribuir* um analisador quando o campo é criado. Isto é verdade tanto para as propriedades do **analisador** como **do indexAnalyzer.** A **propriedade searchAnalyzer** é uma exceção (você pode atribuir esta propriedade a um campo existente). |
| Atualizar ou eliminar uma definição de analisador num índice | Não é possível eliminar ou alterar uma configuração de analisador existente (analisador, tokenizer, filtro de fichas ou filtro de carvão) no índice, a menos que reconstrua todo o índice. |
| Adicione um campo a um sugestivo | Se um campo já existe e pretende adicioná-lo a uma construção [de Suggesters,](index-add-suggesters.md) deve reconstruir o índice. |
| Excluir um campo | Para remover fisicamente todos os vestígios de um campo, tem que reconstruir o índice. Quando uma reconstrução imediata não é prática, pode modificar o código de aplicação para desativar o acesso ao campo "eliminado" ou utilizar o [parâmetro de consulta $select](search-query-odata-select.md) para escolher quais os campos representados no conjunto de resultados. Fisicamente, a definição de campo e o conteúdo permanecem no índice até à próxima reconstrução, quando se aplica um esquema que omite o campo em questão. |
| Níveis de comutação | Se necessitar de mais capacidade, não existe uma atualização no portal Azure. Deve ser criado um novo serviço e os índices devem ser construídos de raiz no novo serviço. Para ajudar a automatizar este processo, pode utilizar o código de amostra **de backup de índices** neste [repo de amostra Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Esta aplicação irá fazer o back up do seu índice para uma série de ficheiros JSON e, em seguida, recriar o índice num serviço de pesquisa que especifica.|

## <a name="update-conditions"></a>Atualizar condições

Muitas outras modificações podem ser feitas sem afetar as estruturas físicas existentes. Especificamente, as seguintes alterações *não* requerem uma reconstrução de índice. Para estas alterações, pode [atualizar uma definição de índice](/rest/api/searchservice/update-index) com as suas alterações.

+ Adicionar um novo campo
+ Desa estade o atributo **recuperável** num campo existente
+ Desaça um **pesquisadoranalzer** num campo existente
+ Adicione uma nova definição de analisador num índice
+ Adicionar, atualizar ou eliminar perfis de pontuação
+ Adicionar, atualizar ou eliminar as definições de CORS
+ Adicionar, atualizar ou eliminar sinónimoMaps

Quando se adiciona um novo campo, os documentos indexados existentes recebem um valor nulo para o novo campo. Numa futura atualização de dados, os valores dos dados de origem externa substituem os nulos adicionados pela Azure Cognitive Search. Para obter mais informações sobre a atualização do conteúdo do índice, consulte [Adicionar, Atualizar ou Eliminar Documentos](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Como reconstruir um índice

Durante o desenvolvimento, o esquema de índice muda frequentemente. Pode planeá-lo criando índices que podem ser eliminados, recriados e recarregados rapidamente com um pequeno conjunto de dados representativos.

Para aplicações já em produção, recomendamos a criação de um novo índice que corre lado a lado um índice existente para evitar o tempo de inatividade da consulta. O seu código de aplicação fornece reorientação para o novo índice.

A indexação não é executada em segundo plano e o serviço equilibrará a indexação adicional contra consultas em curso. Durante a indexação, pode [monitorizar pedidos de consulta](search-monitor-queries.md) no portal para garantir que as consultas estão a ser concluídas em tempo útil.

1. Determinar se é necessária uma reconstrução. Se estiver apenas a adicionar campos, ou a alterar alguma parte do índice que não está relacionado com os campos, poderá simplesmente [atualizar a definição](/rest/api/searchservice/update-index) sem apagar, recriar e recarregá-la totalmente.

1. [Obtenha uma definição de índice](/rest/api/searchservice/get-index) no caso de precisar para referência futura.

1. [Largue o índice existente,](/rest/api/searchservice/delete-index)assumindo que não está a executar índices novos e antigos lado a lado. 

   Quaisquer consultas que visem esse índice são imediatamente retiradas. Lembre-se que a eliminação de um índice é irreversível, destruindo o armazenamento físico para a recolha de campos e outras construções. Faça uma pausa para pensar nas implicações antes de a largar. 

1. [Crie um índice revisto,](/rest/api/searchservice/create-index)onde o corpo do pedido inclui definições de campo alteradas ou modificadas.

1. [Carregue o índice com documentos](/rest/api/searchservice/addupdate-or-delete-documents) de uma fonte externa.

Quando se cria o índice, o armazenamento físico é atribuído para cada campo no esquema de índice, com um índice invertido criado para cada campo pesmável. Os campos que não são pes pesjáveis podem ser utilizados em filtros ou expressões, mas não têm índices invertidos e não são texto completo ou pescatórios difusos. Numa reconstrução de índices, estes índices invertidos são eliminados e recriados com base no esquema de índice que fornece.

Quando carrega o índice, o índice invertido de cada campo é preenchido com todas as palavras únicas e simbólicas de cada documento, com um mapa para os iDs de documento correspondentes. Por exemplo, ao indexar um conjunto de dados de hotéis, um índice invertido criado para um campo city pode conter termos para Seattle, Portland, e assim por diante. Documentos que incluem Seattle ou Portland no campo city teriam o seu documento de identificação listado ao lado do termo. Em qualquer operação [de Adicionar, Atualizar ou Eliminar,](/rest/api/searchservice/addupdate-or-delete-documents) os termos e a lista de ID do documento são atualizados em conformidade.

> [!NOTE]
> Se tiver requisitos rigorosos de SLA, poderá considerar a disponibilização de um novo serviço especificamente para este trabalho, com desenvolvimento e indexação que ocorram em isolamento total de um índice de produção. Um serviço separado funciona no seu próprio hardware, eliminando qualquer possibilidade de contenção de recursos. Quando o desenvolvimento estiver concluído, ou deixaria o novo índice no lugar, redirecionando as consultas para o novo ponto final e índice, ou executaria o código final para publicar um índice revisto no seu serviço original de Pesquisa Cognitiva Azure. Atualmente, não existe qualquer mecanismo para mover um índice pronto a utilizar para outro serviço.

## <a name="check-for-updates"></a>Procurar atualizações

Pode começar a consultar um índice assim que o primeiro documento estiver carregado. Se souber o ID de um documento, a [API do Documento de Procura](/rest/api/searchservice/lookup-document) rest devolve o documento específico. Para testes mais amplos, deve esperar até que o índice esteja totalmente carregado e, em seguida, usar consultas para verificar o contexto que espera ver.

Pode utilizar [o Search Explorer](search-explorer.md) ou uma ferramenta de teste web como o [Postman](search-get-started-rest.md) ou [o Visual Studio Code](search-get-started-vs-code.md) para verificar se há conteúdo atualizado.

Se adicionou ou renomeou um campo, utilize [$select](search-query-odata-select.md) para devolver esse campo: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Veja também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Índice grandes conjuntos de dados em escala](search-howto-large-index.md)
+ [Indexação no portal](search-import-data-portal.md)
+ [Indexante de base de dados Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança em Pesquisa Cognitiva Azure](search-security-overview.md)