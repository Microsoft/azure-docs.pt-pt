---
title: Indexar um conjunto de dados grandes usando indexadores internos-Azure Search
description: Aprenda estratégias para indexação de dados grandes ou indexação computacionalmente intensiva por meio do modo de lote, de origem e de técnicas para indexação agendada, paralela e distribuída.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: heidist
ms.openlocfilehash: 44a8136c4e02d4eceb5b11231bbbfed010159e75
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172884"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Como indexar conjuntos de dados grandes no Azure Search

À medida que os volumes de dados crescem ou as necessidades de processamento mudam, você pode descobrir que estratégias de indexação simples ou padrão não são mais produtivas. Por Azure Search, há várias abordagens para acomodar conjuntos de dados maiores, variando de como você estrutura uma solicitação de carregamento de dados, para usar um indexador específico de origem para cargas de trabalho agendadas e distribuídas.

As mesmas técnicas para dados grandes também se aplicam a processos de longa execução. Em particular, as etapas descritas em [indexação paralela](#parallel-indexing) são úteis para indexação computacionalmente intensiva, como análise de imagem ou processamento de linguagem natural em [pipelines de pesquisa cognitiva](cognitive-search-concept-intro.md).

As seções a seguir exploram três técnicas para indexar grandes quantidades de dados.

## <a name="option-1-pass-multiple-documents"></a>Opção 1: Passar vários documentos

Um dos mecanismos mais simples para indexar um conjunto de dados maior é enviar vários documentos ou registros em uma única solicitação. Desde que toda a carga esteja abaixo de 16 MB, uma solicitação pode lidar com até 1000 documentos em uma operação de upload em massa. Esses limites se aplicam se você estiver usando a [API REST](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou [IndexBatch](https://docs.microsoft.com/otnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) no SDK do .net. Para qualquer uma das APIs, você deve empacotar 1000 documentos no corpo de cada solicitação.

A indexação do lote é implementada para solicitações individuais usando REST ou .NET, ou por meio de indexadores. Alguns indexadores operam sob limites diferentes. Especificamente, a indexação de blob do Azure define o tamanho do lote em 10 documentos no reconhecimento do tamanho médio do documento maior. Para indexadores baseados na [API REST criar indexador](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ), você pode definir o `BatchSize` argumento para personalizar essa configuração para corresponder melhor às características de seus dados. 

> [!NOTE]
> Para manter o tamanho do documento inativo, evite adicionar dados não consultáveis a um índice. Imagens e outros dados binários não são pesquisáveis diretamente e não devem ser armazenados no índice. Para integrar dados não consultáveis nos resultados da pesquisa, você deve definir um campo não pesquisável que armazena uma referência de URL para o recurso.

## <a name="option-2-add-resources"></a>Opção 2: Adicionar recursos

Os serviços provisionados em um dos [tipos de preço padrão](search-sku-tier.md) geralmente têm capacidade subutilizada para armazenamento e cargas de trabalho (consultas ou indexação), o que torna [o aumento da partição e da réplica conta](search-capacity-planning.md) uma solução óbvia para acomodar conjuntos de dados maiores. Para obter melhores resultados, você precisa de ambos os recursos: partições para armazenamento e réplicas para o trabalho de ingestão de dados.

O aumento de réplicas e partições são eventos faturáveis que aumentam seu custo, mas, a menos que você esteja indexando continuamente sob a carga máxima, você pode adicionar escala para a duração do processo de indexação e ajustar os níveis de recursos de volta após a indexação Finaliza.

## <a name="option-3-use-indexers"></a>Opção 3: Usar indexadores

Os [indexadores](search-indexer-overview.md) são usados para rastrear fontes de dados externas em plataformas de dados do Azure com suporte para conteúdo pesquisável. Embora não seja especificamente destinado à indexação em larga escala, vários recursos do indexador são particularmente úteis para acomodar conjuntos de dados maiores:

+ Os agendadores permitem que você distribua a indexação em intervalos regulares para que você possa distribuí-la ao longo do tempo.
+ A indexação agendada pode retomar no último ponto de interrupção conhecido. Se uma fonte de dados não for totalmente rastreada em uma janela de 24 horas, o indexador retomará a indexação no dia dois em qualquer lugar em que parou.
+ O particionamento de dados em fontes de dados individuais menores permite o processamento paralelo. Você pode dividir um conjunto de dados grande em conjuntos de dados menores e, em seguida, criar várias definições de fonte de dados do indexador que podem ser indexadas em paralelo.

> [!NOTE]
> Os indexadores são específicos da fonte de dados, portanto usar uma abordagem de indexador só é viável para fontes de dados selecionadas no Azure: [Banco de dados SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [armazenamento de BLOBs](search-howto-indexing-azure-blob-storage.md), [armazenamento de tabelas](search-howto-indexing-azure-tables.md) [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Indexação agendada

O agendamento do indexador é um mecanismo importante para o processamento de grandes conjuntos de dados, bem como processos de execução lenta, como análise de imagem em um pipeline de pesquisa cognitiva. O processamento do indexador opera em uma janela de 24 horas. Se o processamento não for concluído dentro de 24 horas, os comportamentos do agendamento do indexador poderão funcionar para sua vantagem. 

Por design, a indexação agendada começa em intervalos específicos, com um trabalho geralmente concluído antes de continuar no próximo intervalo agendado. No entanto, se o processamento não for concluído dentro do intervalo, o indexador será interrompido (porque ele ficou sem tempo). No próximo intervalo, o processamento retoma o local em que ele parou, com o sistema mantendo o controle de onde isso acontece. 

Em termos práticos, para cargas de índice que abrangem vários dias, você pode colocar o indexador em uma agenda de 24 horas. Quando a indexação é retomada para o próximo ciclo de 24 horas, ela é reiniciada no último documento válido conhecido. Dessa forma, um indexador pode trabalhar como uma lista de pendências de documento em uma série de dias até que todos os documentos não processados sejam processadas. Para obter mais informações sobre essa abordagem, consulte [indexando grandes conjuntos de dados no armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Para obter mais informações sobre como definir agendas em geral, consulte [criar API REST do indexador](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax) ou veja [como agendar indexadores para Azure Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indexação paralela

Uma estratégia de indexação paralela baseia-se na indexação de várias fontes de dados em harmonia, em que cada definição de fonte de dados especifica um subconjunto dos dados. 

Para requisitos de indexação não rotineiras e de computação intensiva, como o OCR em documentos digitalizados em um pipeline de pesquisa cognitiva, análise de imagem ou processamento de idioma natural – uma estratégia de indexação paralela geralmente é a abordagem certa para a conclusão de um processo de execução demorada no menor tempo. Se você puder eliminar ou reduzir solicitações de consulta, a indexação paralela em um serviço que não está manipulando as consultas simultaneamente é a melhor opção de estratégia para trabalhar em um grande corpo de conteúdo de processamento lento. 

O processamento paralelo tem estes elementos:

+ Subdividir dados de origem entre vários contêineres ou várias pastas virtuais dentro do mesmo contêiner. 
+ Mapeie cada conjunto de dados para sua própria [fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source), emparelhado com seu próprio [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Para pesquisa cognitiva, faça referência ao [mesmo](https://docs.microsoft.com/rest/api/searchservice/create-skillset) conpróprio conpróprio de cada definição de indexador.
+ Gravar no mesmo índice de pesquisa de destino. 
+ Agendar todos os indexadores para execução ao mesmo tempo.

> [!NOTE]
> Azure Search não oferece suporte a réplicas ou partições dedicadas a cargas de trabalho específicas. O risco de uma indexação simultânea pesada é sobrecarregar o sistema para o prejudicial do desempenho da consulta. Se você tiver um ambiente de teste, implemente a indexação paralela primeiro para entender as compensações.

### <a name="how-to-configure-parallel-indexing"></a>Como configurar a indexação paralela

Para indexadores, a capacidade de processamento é flexível com base em um subsistema do indexador para cada SU (unidade de serviço) usada pelo serviço de pesquisa. Vários indexadores simultâneos são possíveis em Azure Search serviços provisionados nas camadas básica ou Standard com pelo menos duas réplicas. 

1. Na [portal do Azure](https://portal.azure.com), na página **visão geral** do painel do serviço de pesquisa, verifique o **tipo de preço** para confirmar se ele pode acomodar a indexação paralela. As camadas básica e standard oferecem várias réplicas.

2. Em **configurações** > **escala**, [aumente as réplicas](search-capacity-planning.md) para processamento paralelo: uma réplica adicional para cada carga de trabalho do indexador. Deixe um número suficiente para o volume de consulta existente. Sacrificar as cargas de trabalho de consulta para indexação não é uma boa desvantagem.

3. Distribua dados em vários contêineres em um nível que Azure Search indexadores possam alcançar. Isso pode ser várias tabelas no banco de dados SQL do Azure, vários contêineres no armazenamento de BLOBs do Azure ou várias coleções. Defina um objeto de fonte de dados para cada tabela ou contêiner.

4. Criar e agendar vários indexadores para execução em paralelo:

   + Suponha um serviço com seis réplicas. Configure seis indexadores, cada um mapeado para uma fonte de dados que contém um sexto do conjunto de dados para uma divisão de 6 vias de todo o conjunto de dados. 

   + Aponte cada indexador para o mesmo índice. Para cargas de trabalho de pesquisa cognitiva, aponte cada indexador para o mesmo ponto de mesmo conhecimento.

   + Dentro de cada definição de indexador, agende o mesmo padrão de execução de tempo de execução. Por exemplo, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` cria um agendamento em 2018-05-15 em todos os indexadores, executando em intervalos de oito horas.

No horário agendado, todos os indexadores iniciam a execução, carregam dados, aplicam aprimoramentos (se você configurou um pipeline de pesquisa cognitiva) e gravando no índice. Azure Search não bloqueia o índice para atualizações. As gravações simultâneas são gerenciadas, com repetição se uma gravação específica não tiver sucesso na primeira tentativa.

> [!Note]
> Ao aumentar as réplicas, considere aumentar a contagem de partições se o tamanho do índice for projetado para aumentar significativamente. Partições armazenam fatias de conteúdo indexado; Quanto mais partições você tiver, menor será a fatia que cada uma terá para armazenar.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Indexação no portal](search-import-data-portal.md)
+ [Indexador de banco de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança em Azure Search](search-security-overview.md)
