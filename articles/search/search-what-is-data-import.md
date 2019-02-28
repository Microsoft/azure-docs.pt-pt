---
title: Importação de dados para ingestão de dados para um índice de pesquisa - Azure Search
description: Preencha e carregar dados para um índice da Azure Search a partir de origens de dados externas.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 85a2810e8ab8de5ad2967aaf17f421d871368063
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958461"
---
# <a name="indexing-external-data-for-queries-in-azure-search"></a>Indexação de dados externos para consultas no Azure Search

No Azure Search, as consultas são executadas nos seus conteúdos carregados para e guardou numa [índice de pesquisa](search-what-is-an-index.md). Este artigo examina as duas abordagens básicas para preencher um índice: *push* seus dados para o índice programaticamente, ou ponto de um [indexador de Azure Search](search-indexer-overview.md) numa origem de dados suportada para  *Extração* nos dados.

Com qualquer uma das abordagens, o objetivo é *carregar dados* de uma origem de dados externos para um índice da Azure Search. O Azure Search permitirá que criar um índice vazio, mas até push ou extrair dados para ele, não é passível de consulta.

## <a name="pushing-data-to-an-index"></a>Enviar dados para um índice
O modelo de envio, utilizado para enviar programaticamente os seus dados para o Azure Search, é a abordagem mais flexível. Em primeiro lugar, não tem restrições quanto ao tipo de origem de dados. Qualquer conjunto de dados composto por documentos JSON pode ser enviado para um índice do Azure Search, pressupondo que cada documento desse conjunto tem campos que sejam mapeados para os campos definidos no esquema de índice. Em segundo lugar, não tem restrições relativamente à frequência de execução. Pode enviar alterações para os índices tantas vezes quanto quiser. Para aplicações com requisitos de latência muito baixos (por exemplo, se quiser que as operações de pesquisa estejam sincronizadas com bases de dados de inventário dinâmicas), o modelo de envio é a única opção.

Esta abordagem é mais flexível do que o modelo de extração, pois pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, consoante o limite que ocorrer primeiro). O modelo de envio também permite carregar documentos para a Azure Search independentemente do local de armazenamento dos dados.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Como enviar dados para um índice do Azure Search

Pode utilizar as APIs seguintes para carregar um ou múltiplos documentos para um índice:

+ [Adicionar, Atualizar ou Eliminar Documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Atualmente não existe qualquer suporte de ferramentas para o envio de dados através do portal.

Para obter uma introdução a cada metodologia, veja [Importar dados com REST](search-import-data-rest-api.md) ou [Importar dados com .NET](search-import-data-dotnet.md).


## <a name="pulling-data-into-an-index"></a>Extrair dados para um índice
O modelo de extração pesquisa uma origem de dados suportada e carrega automaticamente os dados para o seu índice. No Azure Search, esta capacidade é implementada através dos *indexadores*, disponíveis atualmente nestas plataformas:

+ [Armazenamento de blobs](search-howto-indexing-azure-blob-storage.md)
+ [Armazenamento de tabelas](search-howto-indexing-azure-tables.md)
+ [BD do Cosmos para o Azure](https://aka.ms/documentdb-search-indexer)
+ [Base de Dados SQL do Azure e SQL Server em VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Os indexadores ligam índices a uma origem de dados (geralmente, uma tabela, vista ou estrutura equivalente) e mapeiam os campos da origem para os campos equivalentes nos índices. Durante a execução, o conjunto de linhas é automaticamente transformado em JSON e carregado para o índice especificado. Todos os indexadores suportam o agendamento, de modo a que possa especificar a frequência com que os dados devem ser atualizados. A maioria dos indexadores disponibilizam o registo de alterações, se as origens de dados o suportarem. Os indexadores, através do registo de alterações e eliminações aos documentos existentes, além do reconhecimento de novos documentos, suprimem a gestão ativa dos dados no índice. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Como extrair dados para um índice do Azure Search

A funcionalidade de indexador está exposta no [portal do Azure](search-import-data-portal.md), na [API REST](/rest/api/searchservice/Indexer-operations) e no [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Uma vantagem de utilizar o portal é que, geralmente, o Azure Search consegue gerar um esquema de índice predefinido por si, ao ler os metadados do conjunto de dados de origem. Pode modificar o índice gerado até o índice ser processado, após o qual as únicas edições ao esquema permitidas são as que não requerem nova indexação. Se as alterações que quiser fazer influenciarem o esquema diretamente, terá de recriar o índice. 

## <a name="verify-data-import-with-search-explorer"></a>Verifique se a importação de dados com o Explorador de pesquisa

Uma forma rápida de fazer uma verificação preliminar no carregamento do documento é usar **Explorador de pesquisa** no portal. O explorador permite-lhe consultar índices sem ter de escrever qualquer código. A experiência de pesquisa baseia-se em predefinições, como a [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e o [parâmetro de consulta searchMode](/rest/api/searchservice/search-documents) predefinido. Os resultados são devolvidos em JSON, de modo a que possa inspecionar todo o documento.

> [!TIP]
> Vários [exemplos de código do Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluem conjuntos de dados incorporados ou disponíveis a pronto, o que lhe dá uma forma fácil de começar. O portal também disponibiliza um indexador e uma origem de dados de exemplo, que consiste num pequeno conjunto de dados de imobiliário (com o nome “realestate-us-sample"). Ao executar o indexador pré-configurado na origem de dados de exemplo, um índice é criado e carregado com documentos que, em seguida, podem ser consultados no Explorador de pesquisa ou ao código que escreve.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Instruções do portal: criar, carregar e consultar índices](search-get-started-portal.md)
