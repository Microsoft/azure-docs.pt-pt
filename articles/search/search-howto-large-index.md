---
title: Índice conjunto de dados grande com indexadores incorporados - Azure Search
description: Aprenda estratégias para indexação de dados de grandes dimensões ou indexação intensos de computação por meio do modo de lote, obtenção de recursos e técnicas para agendada, paralelas e distribuídas de indexação.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2f3d08a32384cea815f096f51b24eea596d0d118
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871170"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Como indexar grandes conjuntos de dados no Azure Search

À medida que os volumes de dados aumentam ou necessidades de processamento, pode achar que estratégias de indexação predefinida já não são práticas. Para o Azure Search, existem várias abordagens para acomodar os conjuntos de dados maiores, que vão desde a forma como um pedido de carregamento de dados, para utilizar um indexador específicos da fonte para as cargas de trabalho distribuídas e agendadas.

As mesmas técnicas para dados grandes também se aplicam aos processos de longa execução. Em particular, os passos descritos em [indexação paralela](#parallel-indexing) são úteis para indexação computacionalmente intensivas, como análise de imagem ou de processamento em linguagem natural [pipelines de pesquisa cognitiva](cognitive-search-concept-intro.md).

## <a name="batch-indexing"></a>Indexação do batch

Um dos mecanismos mais simples para um conjunto de dados maior de indexação é submeter vários documentos ou registos num único pedido. Desde que a carga inteira é menos de 16 MB, um pedido pode processar até 1000 documentos numa operação de carregamento em massa. Supondo que o [adicionar ou atualizar documentos REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), teria de pacote 1000 documentos no corpo do pedido.

Indexação do batch é implementado para pedidos individuais através do REST ou .NET, ou através de indexadores. Alguns indexadores operam com limites diferente. Especificamente, a indexação de Blobs do Azure define o tamanho do lote em 10 documentos granjeamos o maior tamanho de documentos médio. Para indexadores baseadas o [criar API REST do indexador](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ), pode definir o `BatchSize` argumento para personalizar esta definição para melhor corresponder as características dos seus dados. 

> [!NOTE]
> Para manter o tamanho do documento para baixo, lembre-se de excluir os dados não consultável no pedido. Imagens e outros dados binários não são diretamente pesquisáveis e não devem ser armazenados no índice. Para integrar dados não consultável resultados da pesquisa, deve definir um campo não pesquisável, que armazena uma referência de URL para o recurso.

## <a name="add-resources"></a>Adicionar recursos

Serviços aprovisionados da [padrão de escalões de preço](search-sku-tier.md) geralmente têm subutilizados capacidade para armazenamento e cargas de trabalho (consultas ou indexação), que faz com que [aumentando as contagens de partição e réplica ](search-capacity-planning.md) uma solução óbvia para acomodar os conjuntos de dados maiores. Para obter melhores resultados, terá dos dois recursos: as partições de armazenamento e as réplicas para o trabalho de ingestão de dados.

Cada vez maior de réplicas e partições são eventos a cobrar que aumentam o custo, mas, a menos que estiver indexando continuamente sob carga máxima, pode adicionar dimensionamento durante o processo de indexação e, em seguida, ajustar níveis de recursos para baixo depois de indexação foi concluída.

## <a name="use-indexers"></a>Utilizar indexadores

[Indexadores](search-indexer-overview.md) são utilizados para pesquisar origens de dados externas para conteúdo pesquisável. Embora não especificamente para indexação em grande escala, várias capacidades de indexador são particularmente úteis para acomodar os maiores conjuntos de dados:

+ Pessoas que agendam permite-lhe dividir indexação em intervalos regulares, de modo que possa espalhar ao longo do tempo.
+ Pode retomar a indexação agendada no último ponto de interrupção conhecidos. Se uma origem de dados não é totalmente rastreada durante um período de 24 horas, o indexador será retomada a indexação no dia dois em qualquer lugar onde parou.
+ Criação de partições de dados nas origens de dados individuais mais pequenas permite o processamento paralelo. Pode dividir um grande conjunto de dados em conjuntos de dados menores e, em seguida, criar várias definições de origem de dados que podem ser indexadas em paralelo.

> [!NOTE]
> Indexadores são origem-específicos de dados, portanto, usando uma abordagem de indexador apenas é viável para origens de dados selecionadas no Azure: [Base de dados SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [armazenamento de BLOBs](search-howto-indexing-azure-blob-storage.md), [armazenamento de tabelas](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

## <a name="scheduled-indexing"></a>Agendado a indexação

Agendamento do indexador é um importante mecanismo para processar grandes conjuntos de dados, bem como processos de execução lenta, como a análise de imagem num pipeline de pesquisa cognitiva. Processamento de indexador opera durante um período de 24 horas. Se não for concluída dentro de 24 horas de processamento, os comportamentos de agendamento do indexador podem trabalhar em seu favor. 

Por design, agendada indexação começa em intervalos específicos, com uma tarefa normalmente concluir antes de continuar no próximo intervalo agendado. No entanto, se o processamento não for concluída dentro do intervalo, o indexador será interrompido (porque ele ficou sem tempo). No intervalo seguinte, onde pela última vez parou, com a manutenção do sistema de retoma de processamento de controle de onde o que ocorre. 

Em termos práticos, para cargas de índice que abrangem vários dias, pode colocar o indexador com base numa agenda de 24 horas. Durante a indexação CVS para o próximo ciclo de 24 horas, ele será reiniciada no último documento bom conhecido. Dessa forma, um indexador pode trabalhar sua maneira por meio de uma lista de pendências do documento em relação a uma série de dias até que todos os documentos não processados são processados. Para obter mais informações sobre esta abordagem, consulte [indexação de grandes conjuntos de dados no armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Para obter mais informações sobre como definir agendas em geral, consulte [criar API REST do indexador](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indexação paralela

Um paralelo estratégia de indexação baseia-se de indexar várias fontes de dados em uníssono, em que cada definição de origem de dados especifica um subconjunto dos dados. 

Requisitos de indexação de não ser de rotina, intensos de computação - como OCR em documentos digitalizados num pipeline de pesquisa cognitiva, análise de imagem ou processamento de linguagem natural - um paralelo indexação estratégia é, muitas vezes, a abordagem certa para concluir uma processo de execução demorada no tempo mais curto. Se pode eliminar ou reduzir a pedidos de consulta, a indexação paralela num serviço que não está em simultâneo a processar consultas é sua melhor opção de estratégia para trabalhar com uma grande quantidade de conteúdo de processamento lento. 

Processamento paralelo tem esses elementos:

+ Subdividir a origem de dados entre vários contentores ou várias pastas virtuais dentro do mesmo contêiner. 
+ Mapear cada conjunto de dados de mini para sua própria [origem data](https://docs.microsoft.com/rest/api/searchservice/create-data-source), juntamente a sua própria [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Para a pesquisa cognitiva, referenciar o mesmo [conjunto de capacidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset) em cada definição de indexador.
+ Escreva para o mesmo índice de pesquisa de destino. 
+ Agende todos os indexadores para executar ao mesmo tempo.

> [!NOTE]
> O Azure Search não suporta dedicar réplicas ou partições para cargas de trabalho específicas. O risco de indexação simultâneas pesado é overburdening seu sistema para o dano de desempenho de consulta. Se tiver um ambiente de teste, implemente paralela indexação lá em primeiro lugar para entender as compensações.

### <a name="how-to-configure-parallel-indexing"></a>Como configurar a indexação paralela

Para indexadores, capacidade de processamento é menos baseada num subsistema de indexador para cada unidade de serviço (SU) utilizada pelo seu serviço de pesquisa. Vários indexadores simultâneos são possíveis em serviços Azure Search aprovisionados nos escalões básico ou Standard de ter, pelo menos, duas réplicas. 

1. Na [portal do Azure](https://portal.azure.com), no seu dashboard de serviço de pesquisa **descrição geral** página, verifique o **escalão de preço** para confirmar que pode acomodar a indexação paralela. Escalões básico e Standard oferecem várias réplicas.

2. Na **configurações** > **dimensionamento**, [aumentar réplicas](search-capacity-planning.md) para processamento paralelo: uma réplica adicional para cada carga de trabalho do indexador. Deixe um número suficiente de volume de consulta existente. Sacrificar cargas de trabalho de consulta para indexação não é uma boa troca.

3. Distribua os dados em vários contentores num nível que podem atingir indexadores do Azure Search. Isto pode ser várias tabelas na base de dados do Azure SQL, vários contentores no armazenamento de Blobs do Azure ou várias coleções. Defina um objeto de origem de dados para cada tabela ou o contentor.

4. Criar e agendar vários indexadores para executar em paralelo:

   + Suponha que um serviço com seis réplicas. Configure seis indexadores, cada um deles mapeado para uma origem de dados que contém um sexto do conjunto de dados para uma divisão de 6-vias de todo o conjunto de dados. 

   + Aponte cada indexador para o mesmo índice. Para cargas de trabalho de pesquisa cognitiva, aponte cada indexador para o mesmo conjunto de capacidades.

   + Dentro de cada definição de indexador, agende o mesmo padrão de tempo de execução. Por exemplo, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` cria uma agenda em 2018-05-15 em todos os indexadores, em execução em intervalos de oito horas.

Na hora agendada, todos os indexadores inicia a execução, carregamento de dados, aplicar possível (se tiver configurado um pipeline de pesquisa cognitiva) e escrever para o índice. O Azure Search não bloquear o índice para atualizações. Escritas simultâneas são geridas, com a repetição se uma gravação particular não tiver êxito na primeira tentativa.

> [!Note]
> Ao aumentar as réplicas, considere aumentar o número de partições, se o tamanho do índice, está projetado para aumentar significativamente. Partições armazenam setores de conteúdo indexado; as partições mais tiver, menor será o setor de cada um deles tem de armazenar.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Indexação no portal](search-import-data-portal.md)
+ [Indexador da base de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança no Azure Search](search-security-overview.md)