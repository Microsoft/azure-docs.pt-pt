---
title: Indexar grande conjunto de dados usando indexadores incorporados
titleSuffix: Azure Cognitive Search
description: Estratégias para indexação de dados de grande dimensão ou indexação computacionalmente intensiva através do modo de lote, resourcing e técnicas para indexação programada, paralela e distribuída.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 915243fb4dbc6bb274e26261bc5741811ef24592
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82925988"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Como indexar grandes conjuntos de dados na Pesquisa Cognitiva Azure

A Azure Cognitive Search suporta [duas abordagens básicas](search-what-is-data-import.md) para importar dados num índice de pesquisa: *empurrar* os seus dados para o índice programáticamente, ou apontar um [indexador](search-indexer-overview.md) de pesquisa cognitiva Azure numa fonte de dados suportada para *puxar* os dados.

À medida que os volumes de dados crescem ou o processamento precisa de mudanças, pode descobrir que estratégias de indexação simples ou padrão já não são práticas. Para a Pesquisa Cognitiva Azure, existem várias abordagens para acomodar conjuntos de dados maiores, desde a forma como estrutura um pedido de upload de dados, até a utilização de um indexante específico de fonte para cargas de trabalho programadas e distribuídas.

As mesmas técnicas aplicam-se também a processos de longa duração. Em particular, os passos delineados na [indexação paralela](#parallel-indexing) são úteis para a indexação computacionalmente intensiva, como a análise de imagem ou o processamento de linguagem natural num gasoduto de enriquecimento de [IA.](cognitive-search-concept-intro.md)

As secções seguintes exploram técnicas para indexar grandes quantidades de dados usando tanto a API push como os indexers.

## <a name="push-api"></a>Empurrar API

Ao empurrar os dados para um índice, há várias considerações-chave que impactam as velocidades de indexação para a API push. Estes fatores estão descritos na secção abaixo. 

Além da informação neste artigo, também pode tirar partido das amostras de código no tutorial de velocidades de [indexação otimizante](tutorial-optimize-indexing-push-api.md) para saber mais.

### <a name="service-tier-and-number-of-partitionsreplicas"></a>Nível de serviço e número de divisórias/réplicas

Adicionar divisórias ou aumentar o nível do seu serviço de pesquisa aumentará as velocidades de indexação.

Adicionar réplicas adicionais também pode aumentar as velocidades de indexação, mas não está garantida. Por outro lado, réplicas adicionais aumentarão o volume de consulta que o seu serviço de pesquisa pode suportar. As réplicas são também um componente chave para obter um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Antes de adicionar divisória/réplicas ou upgrade para um nível mais elevado, considere o custo monetário e o tempo de atribuição. A adição de divisórias pode aumentar significativamente a velocidade de indexação, mas adicioná-las/removê-las pode demorar entre 15 minutos e várias horas. Para mais informações, consulte a documentação sobre a [capacidade de ajuste](search-capacity-planning.md).

### <a name="index-schema"></a>Índice Schema

O esquema do seu índice desempenha um papel importante na indexação de dados. A adição de campos e a adição de propriedades adicionais a esses campos (como *pesquisável,* *faceta,* ou *filtrante*) reduzem as velocidades de indexação.

Em geral, recomendamos apenas adicionar propriedades adicionais aos campos se pretender usá-las.

> [!NOTE]
> Para manter o tamanho do documento baixo, evite adicionar dados não-consultados a um índice. As imagens e outros dados binários não são diretamente pesquisáveis e não devem ser armazenados no índice. Para integrar dados não consultados em resultados de pesquisa, deve definir um campo não pesquisável que armazene uma referência URL ao recurso.

### <a name="batch-size"></a>Tamanho do lote

Um dos mecanismos mais simples para indexar um conjunto de dados maior é submeter vários documentos ou registos num único pedido. Desde que toda a carga útil seja inferior a 16 MB, um pedido pode lidar com até 1000 documentos numa operação de upload a granel. Estes limites aplicam-se quer esteja a utilizar a API add [documents REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou o [método Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) no .NET SDK. Para qualquer API, você embalaria 1000 documentos no corpo de cada pedido.

A utilização de lotes para indexar documentos melhorará significativamente o desempenho da indexação. Determinar o tamanho ideal do lote para os seus dados é um componente chave para otimizar velocidades de indexação. Os dois fatores primários que influenciam o tamanho ideal do lote são:
+ O esquema do seu índice
+ O tamanho dos seus dados

Como o tamanho ideal do lote depende do seu índice e dos seus dados, a melhor abordagem é testar diferentes tamanhos de lote para determinar o que resulta nas velocidades de indexação mais rápidas para o seu cenário. Este [tutorial](tutorial-optimize-indexing-push-api.md) fornece código de amostra para testar tamanhos de lote utilizando o .NET SDK. 

### <a name="number-of-threadsworkers"></a>Número de fios/trabalhadores

Para tirar o máximo partido das velocidades de indexação da Azure Cognitive Search, provavelmente terá de usar vários fios para enviar pedidos de indexação de lotes simultaneamente para o serviço.  

O número ideal de fios é determinado por:

+ O nível do seu serviço de pesquisa
+ O número de divisórias
+ O tamanho dos seus lotes
+ O esquema do seu índice

Pode modificar esta amostra e testar com diferentes contagens de fio para determinar a contagem ideal de fio para o seu cenário. No entanto, desde que tenha vários fios a funcionar em simultâneo, deverá poder tirar partido da maioria dos ganhos de eficiência. 

> [!NOTE]
> À medida que aumenta o nível do seu serviço de pesquisa ou aumenta as divisórias, também deve aumentar o número de fios simultâneos.

À medida que aumenta os pedidos que atingem o serviço de pesquisa, poderá encontrar [códigos](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) de estado HTTP indicando que o pedido não teve sucesso. Durante a indexação, dois códigos comuns de estado http são:

+ **503 Serviço Indisponível** - Este erro significa que o sistema está sob carga pesada e o seu pedido não pode ser processado neste momento.
+ **207 Multi-Status** - Este erro significa que alguns documentos foram bem sucedidos, mas pelo menos um falhou.

### <a name="retry-strategy"></a>Estratégia de repetição 

Se ocorrer uma falha, os pedidos devem ser novamente julgados utilizando uma estratégia exponencial de [retry backoff](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

O SDK .NET da Azure Cognitive Search retenta automaticamente 503s e outros pedidos falhados, mas terá de implementar a sua própria lógica para voltar a tentar 207s. Ferramentas de código aberto como [a Polly](https://github.com/App-vNext/Polly) também podem ser usadas para implementar uma estratégia de retry.

### <a name="network-data-transfer-speeds"></a>Velocidades de transferência de dados de rede

As velocidades de transferência de dados da rede podem ser um fator limitativo ao indexar dados. Indexar dados de dentro do seu ambiente Azure é uma maneira fácil de acelerar a indexação.

## <a name="indexers"></a>Indexadores

[Os indexadores](search-indexer-overview.md) são usados para rastejar fontes de dados suportadas do Azure para conteúdo pesquisável. Embora não se destine especificamente a indexação em larga escala, várias capacidades indexantes são particularmente úteis para acomodar conjuntos de dados maiores:

+ Os programadores permitem-lhe empacotar a indexação em intervalos regulares para que possa espalhá-la ao longo do tempo.
+ A indexação programada pode ser retomada no último ponto de paragem conhecido. Se uma fonte de dados não estiver totalmente rastejada dentro de uma janela de 24 horas, o indexante retomará a indexação no segundo dia onde quer que tenha parado.
+ A partilha de dados em fontes de dados individuais mais pequenas permite o processamento paralelo. Pode dividir os dados de origem em componentes menores, como em vários recipientes no armazenamento do Azure Blob, e depois criar [objetos](https://docs.microsoft.com/rest/api/searchservice/create-data-source) de origem de dados correspondentes e múltiplos na Pesquisa Cognitiva Azure que podem ser indexados em paralelo.

> [!NOTE]
> Os indexadores são específicos da fonte de dados, pelo que a utilização de uma abordagem indexante só é viável para fontes de dados selecionadas em Azure: Base de [Dados SQL,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) [Armazenamento blob,](search-howto-indexing-azure-blob-storage.md)armazenamento de [mesa,](search-howto-indexing-azure-tables.md) [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="batch-size"></a>Tamanho do lote

Tal como acontece com a API push, os indexadores permitem configurar o número de itens por lote. Para os indexadores com base na [Create Indexer REST API,](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)pode definir o `batchSize` argumento para personalizar esta definição de forma a corresponder melhor às características dos seus dados. 

Os tamanhos padrão do lote são específicos da fonte de dados. A Base de Dados Azure SQL e a Azure Cosmos DB têm um lote padrão de 1000. Em contraste, a indexação do Azure Blob define o tamanho do lote em 10 documentos em reconhecimento do tamanho médio do documento. 

### <a name="scheduled-indexing"></a>Indexação programada

O agendamento de indexantes é um mecanismo importante para o processamento de grandes conjuntos de dados, bem como processos de execução lenta, como a análise de imagem num pipeline de pesquisa cognitiva. O processamento do indexante funciona dentro de uma janela de 24 horas. Se o processamento não terminar dentro de 24 horas, os comportamentos de agendamento do indexante podem funcionar a seu favor. 

Por design, a indexação programada começa em intervalos específicos, com um trabalho tipicamente concluído antes de retomar no intervalo agendado seguinte. No entanto, se o processamento não estiver concluído dentro do intervalo, o indexante para (porque ficou sem tempo). No intervalo seguinte, o processamento retoma onde ficou parado pela última vez, com o sistema a acompanhar onde isso ocorre. 

Em termos práticos, para cargas de índice que se estendem por vários dias, pode colocar o indexador num horário de 24 horas. Quando a indexação retoma para o próximo ciclo de 24 horas, reinicia no último bom documento conhecido. Desta forma, um indexante pode fazer o seu caminho através de um atraso de documento ao longo de uma série de dias até que todos os documentos não processados sejam processados. Para obter mais informações sobre esta abordagem, consulte [Indexing grandes conjuntos de dados no armazenamento de Blob Azure](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Para obter mais informações sobre a definição de horários em geral, consulte [Create Indexer REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) ou veja [Como agendar indexadores para pesquisa cognitiva Azure](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indexação paralela

Uma estratégia de indexação paralela baseia-se na indexação de múltiplas fontes de dados em uníssono, onde cada definição de fonte de dados especifica um subconjunto dos dados. 

Para requisitos de indexação não rotineiros e computacionalmente intensivos - como o OCR em documentos digitalizados num pipeline de pesquisa cognitiva, análise de imagem ou processamento de linguagem natural - uma estratégia de indexação paralela é muitas vezes a abordagem certa para concluir um processo de longo prazo no menor tempo. Se conseguir eliminar ou reduzir pedidos de consulta, indexar paralelamente um serviço que não esteja a lidar simultaneamente com consultas é a sua melhor opção de estratégia para trabalhar através de um grande corpo de conteúdos de processamento lento. 

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
