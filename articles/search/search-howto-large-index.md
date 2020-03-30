---
title: Indexar grande conjunto de dados usando indexadores incorporados
titleSuffix: Azure Cognitive Search
description: Estratégias para indexação de dados de grande dimensão ou indexação computacionalmente intensiva através do modo de lote, resourcing e técnicas para indexação programada, paralela e distribuída.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190966"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Como indexar grandes conjuntos de dados na Pesquisa Cognitiva Azure

À medida que os volumes de dados crescem ou o processamento precisa de mudanças, pode descobrir que estratégias de indexação simples ou padrão já não são práticas. Para a Pesquisa Cognitiva Azure, existem várias abordagens para acomodar conjuntos de dados maiores, desde a forma como estrutura um pedido de upload de dados, até a utilização de um indexante específico de fonte para cargas de trabalho programadas e distribuídas.

As mesmas técnicas aplicam-se também a processos de longa duração. Em particular, os passos delineados na [indexação paralela](#parallel-indexing) são úteis para a indexação computacionalmente intensiva, como a análise de imagem ou o processamento de linguagem natural num gasoduto de enriquecimento de [IA.](cognitive-search-concept-intro.md)

As seguintes secções exploram três técnicas para indexar grandes quantidades de dados.

## <a name="option-1-pass-multiple-documents"></a>Opção 1: Passar vários documentos

Um dos mecanismos mais simples para indexar um conjunto de dados maior é submeter vários documentos ou registos num único pedido. Desde que toda a carga útil seja inferior a 16 MB, um pedido pode lidar com até 1000 documentos numa operação de upload a granel. Estes limites aplicam-se quer esteja a utilizar a [API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) de Add Documents REST ou o [método Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) no .NET SDK. Para qualquer API, você embalaria 1000 documentos no corpo de cada pedido.

A indexação do lote é implementada para pedidos individuais utilizando REST ou .NET, ou através de indexadores. Alguns indexadores operam sob limites diferentes. Especificamente, a indexação do Azure Blob define o tamanho do lote em 10 documentos em reconhecimento do tamanho médio do documento. Para os indexadores com base na [Create Indexer REST API,](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)pode definir o `BatchSize` argumento para personalizar esta definição de forma a corresponder melhor às características dos seus dados. 

> [!NOTE]
> Para manter o tamanho do documento baixo, evite adicionar dados não-consultados a um índice. As imagens e outros dados binários não são diretamente pesquisáveis e não devem ser armazenados no índice. Para integrar dados não consultados em resultados de pesquisa, deve definir um campo não pesquisável que armazene uma referência URL ao recurso.

## <a name="option-2-add-resources"></a>Opção 2: Adicionar recursos

Os serviços que são prestados num dos níveis de [preços Standard](search-sku-tier.md) têm frequentemente uma capacidade subutilizada tanto para armazenamento como para cargas de trabalho (consultas ou indexação), o que faz com que o aumento da partilha e da réplica [conta uma](search-capacity-planning.md) solução óbvia para acomodar conjuntos de dados maiores. Para obter os melhores resultados, precisa de ambos os recursos: divisórias para armazenamento e réplicas para o trabalho de ingestão de dados.

O aumento de réplicas e divisórias são eventos de faturação que aumentam o seu custo, mas a menos que esteja continuamente indexando sob a carga máxima, pode adicionar escala durante a duração do processo de indexação, e depois ajustar os níveis de recursos para baixo após a indexação terminado.

## <a name="option-3-use-indexers"></a>Opção 3: Utilizar indexadores

[Os indexadores](search-indexer-overview.md) são usados para rastejar fontes de dados suportadas do Azure para conteúdo pesquisável. Embora não se destine especificamente a indexação em larga escala, várias capacidades indexantes são particularmente úteis para acomodar conjuntos de dados maiores:

+ Os programadores permitem-lhe empacotar a indexação em intervalos regulares para que possa espalhá-la ao longo do tempo.
+ A indexação programada pode ser retomada no último ponto de paragem conhecido. Se uma fonte de dados não estiver totalmente rastejada dentro de uma janela de 24 horas, o indexante retomará a indexação no segundo dia onde quer que tenha parado.
+ A partilha de dados em fontes de dados individuais mais pequenas permite o processamento paralelo. Pode dividir os dados de origem em componentes menores, como em vários recipientes no armazenamento do Azure Blob, e depois criar [objetos](https://docs.microsoft.com/rest/api/searchservice/create-data-source) de origem de dados correspondentes e múltiplos na Pesquisa Cognitiva Azure que podem ser indexados em paralelo.

> [!NOTE]
> Os indexadores são específicos da fonte de dados, pelo que a utilização de uma abordagem indexante só é viável para fontes de dados selecionadas em Azure: Base de [Dados SQL,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) [Armazenamento blob,](search-howto-indexing-azure-blob-storage.md)armazenamento de [mesa,](search-howto-indexing-azure-tables.md) [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Indexação programada

O agendamento de indexantes é um mecanismo importante para o processamento de grandes conjuntos de dados, bem como processos de execução lenta, como a análise de imagem num pipeline de pesquisa cognitiva. O processamento do indexante funciona dentro de uma janela de 24 horas. Se o processamento não terminar dentro de 24 horas, os comportamentos de agendamento do indexante podem funcionar a seu favor. 

Por design, a indexação programada começa em intervalos específicos, com um trabalho tipicamente concluído antes de retomar no intervalo agendado seguinte. No entanto, se o processamento não estiver concluído dentro do intervalo, o indexante para (porque ficou sem tempo). No intervalo seguinte, o processamento retoma onde ficou parado pela última vez, com o sistema a acompanhar onde isso ocorre. 

Em termos práticos, para cargas de índice que se estendem por vários dias, pode colocar o indexador num horário de 24 horas. Quando a indexação retoma para o próximo ciclo de 24 horas, reinicia no último bom documento conhecido. Desta forma, um indexante pode fazer o seu caminho através de um atraso de documento ao longo de uma série de dias até que todos os documentos não processados sejam processados. Para obter mais informações sobre esta abordagem, consulte [Indexing grandes conjuntos de dados no armazenamento de Blob Azure](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Para obter mais informações sobre a definição de horários em geral, consulte [Create Indexer REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) ou veja [Como agendar indexadores para pesquisa cognitiva Azure](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indexação paralela

Uma estratégia de indexação paralela baseia-se na indexação de múltiplas fontes de dados em uníssono, onde cada definição de fonte de dados especifica um subconjunto dos dados. 

Para requisitos de indexação não rotineiros e computacionalmente intensivos - como o OCR em documentos digitalizados num pipeline de pesquisa cognitiva, análise de imagem ou processamento de linguagem natural - uma estratégia de indexação paralela é muitas vezes a abordagem certa para completar um processo de longo prazo no menor tempo. Se conseguir eliminar ou reduzir pedidos de consulta, indexar paralelamente um serviço que não esteja a lidar simultaneamente com consultas é a sua melhor opção de estratégia para trabalhar através de um grande corpo de conteúdos de processamento lento. 

O processamento paralelo tem estes elementos:

+ Subdivida os dados de origem entre vários contentores ou várias pastas virtuais dentro do mesmo recipiente. 
+ Mapeie cada mini conjunto de dados para a sua própria fonte de [dados,](https://docs.microsoft.com/rest/api/searchservice/create-data-source)emparelhado com o seu próprio [indexante.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ Para pesquisa cognitiva, referência a mesma [habilidade](https://docs.microsoft.com/rest/api/searchservice/create-skillset) em cada definição indexante.
+ Escreva no mesmo índice de pesquisa de alvo. 
+ Agende todos os indexers para funcionar ao mesmo tempo.

> [!NOTE]
> Na Pesquisa Cognitiva Azure, não é possível atribuir réplicas individuais ou divisórias a indexação ou processamento de consultas. O sistema determina como os recursos são usados. Para compreender o impacto no desempenho da consulta, pode tentar indexação paralela num ambiente de teste antes de o transformar em produção.  

### <a name="how-to-configure-parallel-indexing"></a>Como configurar a indexação paralela

Para os indexadores, a capacidade de processamento baseia-se vagamente num subsistema indexante para cada unidade de serviço (SU) utilizado pelo seu serviço de pesquisa. Vários indexantes simultâneos são possíveis em serviços de Pesquisa Cognitiva Azure aprovisionados em níveis Básicoou Standard com pelo menos duas réplicas. 

1. No [portal Azure,](https://portal.azure.com)na página de **visão geral** do painel de pesquisa, verifique o **nível** de preços para confirmar que pode acomodar indexação paralela. Tanto os níveis Básico como Standard oferecem múltiplas réplicas.

2. Na**Escala de** **Definições,** >  [aumente as réplicas](search-capacity-planning.md) para o processamento paralelo: uma réplica adicional para cada carga de trabalho do indexante. Deixe um número suficiente para o volume de consulta existente. Sacrificar as cargas de trabalho para indexação não é uma boa troca.

3. Distribua dados em vários recipientes a um nível que os indexadores de Pesquisa Cognitiva Azure podem alcançar. Estas podem ser várias tabelas na Base de Dados Azure SQL, vários contentores no armazenamento Azure Blob, ou várias coleções. Defina um objeto de origem de dados para cada tabela ou recipiente.

4. Criar e agendar vários indexadores para funcionar em paralelo:

   + Assuma um serviço com seis réplicas. Configure seis indexantes, cada um mapeado para uma fonte de dados contendo um sexto do conjunto de dados para uma divisão de 6 vias de todo o conjunto de dados. 

   + Aponte cada indexante para o mesmo índice. Para cargas de trabalho de pesquisa cognitiva, aponte cada indexador para o mesmo skillset.

   + Dentro de cada definição de indexante, agende o mesmo padrão de execução a tempo. Por exemplo, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` cria um horário em 2018-05-15 em todos os indexadores, funcionando em intervalos de oito horas.

Na hora programada, todos os indexadores iniciam a execução, carregam dados, aplicam enriquecimentos (se configurar um pipeline de pesquisa cognitiva) e escrever ao índice. A Pesquisa Cognitiva Azure não bloqueia o índice para atualizações. As escritas simultâneas são geridas, com retry se uma determinada escrita não for bem sucedida na primeira tentativa.

> [!Note]
> Ao aumentar as réplicas, considere aumentar a contagem de divisórias se o tamanho do índice for projetado para aumentar significativamente. Divisórias armazenam fatias de conteúdo indexado; quanto mais divisórias tiver, menor é a fatia que cada uma tem para armazenar.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Indexação no portal](search-import-data-portal.md)
+ [Indexador de base de dados Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança na Pesquisa Cognitiva Azure](search-security-overview.md)
