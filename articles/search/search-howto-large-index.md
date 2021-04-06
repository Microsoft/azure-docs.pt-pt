---
title: Índice grande conjunto de dados usando indexantes incorporados
titleSuffix: Azure Cognitive Search
description: Estratégias para a indexação de dados grandes ou indexação computacionalmente intensiva através do modo de lote, resourcing e técnicas para indexação programada, paralela e distribuída.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: b4f54aff78526ba52e56ed9f4cf1feddf40fa69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358397"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Como indexar grandes conjuntos de dados na Pesquisa Cognitiva do Azure

A Azure Cognitive Search suporta [duas abordagens básicas](search-what-is-data-import.md) para importar dados para um índice de pesquisa: *empurrar* os seus dados para o índice programáticamente, ou apontar um [indexante de Pesquisa Cognitiva Azure](search-indexer-overview.md) numa fonte de dados suportada para *obter* os dados.

À medida que os volumes de dados crescem ou as necessidades de processamento mudam, poderá descobrir que estratégias de indexação simples ou padrão já não são práticas. Para a Azure Cognitive Search, existem várias abordagens para acomodar conjuntos de dados maiores, desde a forma como estrutura um pedido de upload de dados, até à utilização de um indexante específico de origem para cargas de trabalho programadas e distribuídas.

As mesmas técnicas também se aplicam a processos de longa duração. Em especial, os passos delineados na [indexação paralela](#parallel-indexing) são úteis para a indexação computacionalmente intensiva, como a análise de imagem ou o processamento de linguagem natural num [gasoduto de enriquecimento de IA](cognitive-search-concept-intro.md).

As secções seguintes exploram técnicas para indexar grandes quantidades de dados usando tanto a API push como os indexantes.

## <a name="use-the-push-api"></a>Use a API push

Ao empurrar dados para um índice utilizando a [API de Add Documents REST](/rest/api/searchservice/addupdate-or-delete-documents) ou o método [IndexDocuments,](/dotnet/api/azure.search.documents.searchclient.indexdocuments)existem várias considerações fundamentais que impactam a velocidade de indexação. Estes fatores estão delineados na secção abaixo, e vão desde a definição da capacidade de serviço até otimizações de códigos.

Para obter mais informações e amostras de código que ilustram a indexação do modelo de impulso, consulte [Tutorial: Otimize as velocidades de indexação](tutorial-optimize-indexing-push-api.md).

### <a name="capacity-of-your-service"></a>Capacidade do seu serviço

Como primeiro passo, reveja as características e [limites](search-limits-quotas-capacity.md) do nível em que adquirou o serviço. Um dos principais fatores diferenciadores entre os níveis de preços é o tamanho e a velocidade das divisórias, que tem um impacto direto na velocidade de indexação. Se forte o seu serviço de pesquisa num nível insuficiente para a carga de trabalho, a atualização para um novo nível pode ser a solução mais fácil e eficaz para aumentar a produção de indexação.

Uma vez satisfeito com o nível, o próximo passo pode ser aumentar o número de divisórias. A atribuição de divisórias pode ser reajustada para baixo após uma primeira corrida de indexação para reduzir o custo global de funcionamento do serviço.

> [!NOTE]
> A adição de réplicas adicionais também pode aumentar as velocidades de indexação, mas não está garantida. Por outro lado, réplicas adicionais aumentarão o volume de consulta que o seu serviço de pesquisa pode suportar. As réplicas são também um componente chave para obter um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
>
> Antes de adicionar divisórias/réplicas ou de upgrade para um nível mais elevado, considere o custo monetário e o tempo de alocação. A adição de divisórias pode aumentar significativamente a velocidade de indexação, mas a adição/remoção pode demorar entre 15 minutos e várias horas. Para obter mais informações, consulte a documentação sobre [o ajuste da capacidade.](search-capacity-planning.md)
>

### <a name="review-index-schema"></a>Comentário de esquema de índice

O esquema do seu índice desempenha um papel importante na indexação de dados. Quanto mais campos tiver, e mais propriedades você definir (como *pescável,* *facetable*, ou *filtrado*) todos contribuem para o aumento do tempo de indexação. Em geral, deve apenas criar e especificar os campos de que realmente precisa num índice de pesquisa.

> [!NOTE]
> Para manter o tamanho do documento baixo, evite adicionar dados não-questionáveis a um índice. As imagens e outros dados binários não são diretamente pespáveis e não devem ser armazenados no índice. Para integrar dados não-questionáveis nos resultados da pesquisa, deve definir um campo não pespável que armazena uma referência URL ao recurso.

### <a name="check-the-batch-size"></a>Verifique o tamanho do lote

Um dos mecanismos mais simples para a indexação de um conjunto de dados maior é apresentar vários documentos ou registos num único pedido. Enquanto toda a carga útil for inferior a 16 MB, um pedido pode lidar com até 1000 documentos numa operação de upload a granel. Estes limites aplicam-se quer esteja a utilizar o [Add Documents REST API](/rest/api/searchservice/addupdate-or-delete-documents) ou o [método IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) no .NET SDK. Para qualquer um dos pedidos, embale 1000 documentos no corpo de cada pedido.

A utilização de lotes para indexar documentos melhorará significativamente o desempenho da indexação. Determinar o tamanho ideal do lote para os seus dados é um componente chave para otimizar as velocidades de indexação. Os dois fatores primários que influenciam o tamanho ideal do lote são:

+ O esquema do seu índice
+ O tamanho dos seus dados

Como o tamanho ideal do lote depende do seu índice e dos seus dados, a melhor abordagem é testar diferentes tamanhos de lote para determinar quais os resultados nas velocidades de indexação mais rápidas para o seu cenário. Este [tutorial](tutorial-optimize-indexing-push-api.md) fornece código de amostra para testar tamanhos de lote usando o .NET SDK. 

### <a name="number-of-threadsworkers"></a>Número de fios/trabalhadores

Para tirar o máximo partido das velocidades de indexação da Azure Cognitive Search, provavelmente terá de usar vários fios para enviar pedidos de indexação de lotes simultaneamente ao serviço.  

O número ideal de fios é determinado por:

+ O nível do seu serviço de pesquisa
+ O número de divisórias
+ O tamanho dos seus lotes
+ O esquema do seu índice

Pode modificar esta amostra e testar com diferentes contagens de fios para determinar a contagem ideal de roscas para o seu cenário. No entanto, desde que tenha vários fios a funcionar simultaneamente, deverá poder tirar partido da maioria dos ganhos de eficiência. 

> [!NOTE]
> À medida que aumenta o nível do seu serviço de pesquisa ou aumenta as divisórias, também deverá aumentar o número de fios simultâneos.

À medida que aumenta os pedidos que atingem o serviço de pesquisa, poderá encontrar [códigos de estado HTTP](/rest/api/searchservice/http-status-codes) que indiquem que o pedido não foi totalmente bem sucedido. Durante a indexação, dois códigos de estado HTTP comuns são:

+ **503 Serviço Indisponível** - Este erro significa que o sistema está sob carga pesada e o seu pedido não pode ser processado neste momento.
+ **207 Multi-Status** - Este erro significa que alguns documentos foram bem sucedidos, mas pelo menos um falhou.

### <a name="retry-strategy"></a>Estratégia de repetição

Se uma falha acontecer, os pedidos devem ser novamente julgados utilizando uma [estratégia de retrocesso exponencial](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Azure Cognitive Search.NET SDK retrifica automaticamente 503s e outros pedidos falhados, mas terá de implementar a sua própria lógica para voltar a tentar 207s. Ferramentas de código aberto como [Polly](https://github.com/App-vNext/Polly) também podem ser usadas para implementar uma estratégia de relíndi.

### <a name="network-data-transfer-speeds"></a>Velocidades de transferência de dados de rede

As velocidades de transferência de dados de rede podem ser um fator limitativo ao indexar dados. A indexação de dados dentro do seu ambiente Azure é uma forma fácil de acelerar a indexação.

## <a name="use-indexers-pull-api"></a>Utilizar indexantes (puxar API)

[Os indexantes](search-indexer-overview.md) são usados para rastrear fontes de dados suportadas pelo Azure para conteúdo pesquisável. Embora não se destinem especificamente a uma indexação em larga escala, várias capacidades de indexante são particularmente úteis para acomodar conjuntos de dados maiores:

+ Os agendadores permitem-lhe empacotar a indexação a intervalos regulares para que possa espalhá-lo ao longo do tempo.
+ A indexação programada pode ser retomada no último ponto de paragem conhecido. Se uma fonte de dados não estiver totalmente rastreada dentro de uma janela de 24 horas, o indexante retomará a indexação no segundo dia em onde quer que tenha deixado de lado.
+ A divisão de dados em fontes de dados individuais mais pequenas permite o processamento paralelo. Pode dividir dados de origem em componentes menores, como em vários contentores no armazenamento de Azure Blob, e depois criar objetos de origem de [dados correspondentes](/rest/api/searchservice/create-data-source) na Pesquisa Cognitiva Azure que podem ser indexados em paralelo.

> [!NOTE]
> Os indexantes são específicos de dados, pelo que a utilização de uma abordagem indexante só é viável para fontes de dados selecionadas no Azure: [SQL Database,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) [Blob storage,](search-howto-indexing-azure-blob-storage.md) [Table storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="check-the-batchsize-argument-on-create-indexer"></a>Verifique o argumento do loteSize no Create Indexer

Tal como acontece com a API push, os indexantes permitem-lhe configurar o número de itens por lote. Para indexadores com base na [API Do Bloco de Índices,](/rest/api/searchservice/Create-Indexer)pode definir o `batchSize` argumento para personalizar esta definição para melhor corresponder às características dos seus dados. 

Os tamanhos predefinidos do lote são específicos da fonte de dados. Azure SQL Database e Azure Cosmos DB têm um tamanho padrão de lote de 1000. Em contraste, a indexação Azure Blob define o tamanho do lote em 10 documentos em reconhecimento do tamanho médio do documento maior. 

### <a name="scheduled-indexing"></a>Indexação programada

O agendamento do indexante é um mecanismo importante para o processamento de grandes conjuntos de dados, bem como processos de marcha lenta como a análise de imagem num pipeline de pesquisa cognitiva. O processamento do indexante funciona dentro de uma janela de 24 horas. Se o processamento não terminar dentro de 24 horas, os comportamentos do agendamento do indexante podem funcionar a seu favor. 

Por design, a indexação programada começa em intervalos específicos, com um trabalho tipicamente concluído antes de retomar no intervalo agendado seguinte. No entanto, se o processamento não estiver concluído dentro do intervalo, o indexante para (porque ficou sem tempo). No intervalo seguinte, o processamento retoma onde ficou pela última vez, com o sistema a acompanhar onde isso ocorre. 

Em termos práticos, para cargas de índices que se estendem por vários dias, pode colocar o indexante num horário de 24 horas. Quando a indexação retoma para o próximo ciclo de 24 horas, reinicia no último bom documento conhecido. Desta forma, um indexante pode trabalhar o seu caminho através de um atraso de documento ao longo de uma série de dias até que todos os documentos não processados sejam processados. Para obter mais informações sobre a definição de horários em geral, consulte [a API do Indexer REST](/rest/api/searchservice/Create-Indexer) ou consulte [Como agendar indexadores para a Pesquisa Cognitiva Azure](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indexação paralela

Uma estratégia de indexação paralela baseia-se na indexação de múltiplas fontes de dados em uníssono, onde cada definição de fonte de dados especifica um subconjunto dos dados. 

Para os requisitos de indexação não rotineiros e computacionalmente intensivos - como o OCR em documentos digitalizados num pipeline de pesquisa cognitiva, análise de imagem ou processamento de linguagem natural - uma estratégia de indexação paralela é muitas vezes a abordagem certa para concluir um processo de longo prazo no menor tempo. Se puder eliminar ou reduzir pedidos de consulta, a indexação paralela num serviço que não esteja simultaneamente a lidar com consultas é a sua melhor opção de estratégia para trabalhar através de um grande conjunto de conteúdos de processamento lento. 

O processamento paralelo tem estes elementos:

+ Subdivide dados de origem entre vários contentores ou várias pastas virtuais dentro do mesmo recipiente. 
+ Mapear cada mini conjunto de dados para a sua própria [fonte de dados,](/rest/api/searchservice/create-data-source)emparelhado com o seu próprio [indexante.](/rest/api/searchservice/create-indexer)
+ Para pesquisa cognitiva, refira-se o mesmo [skillset](/rest/api/searchservice/create-skillset) em cada definição indexante.
+ Escreva no mesmo índice de pesquisa de alvos. 
+ Agende todos os indexantes para funcionar ao mesmo tempo.

> [!NOTE]
> Na Pesquisa Cognitiva Azure, não é possível atribuir réplicas ou divisórias individuais à indexação ou processamento de consultas. O sistema determina como os recursos são usados. Para compreender o impacto no desempenho da consulta, pode tentar a indexação paralela num ambiente de teste antes de o lançar na produção.  

### <a name="how-to-configure-parallel-indexing"></a>Como configurar a indexação paralela

Para os indexantes, a capacidade de processamento baseia-se vagamente num subsistema indexante para cada unidade de serviço (SU) utilizada pelo seu serviço de pesquisa. Vários indexantes simultâneos são possíveis nos serviços de Pesquisa Cognitiva Azure aprovisionados em níveis Básicos ou Standard com pelo menos duas réplicas. 

1. No [portal Azure](https://portal.azure.com), na página **geral** do seu serviço de pesquisa, consulte o **nível de Preços** para confirmar que pode acomodar indexação paralela. Os níveis Básico e Standard oferecem múltiplas réplicas.

2. Pode executar tantos indexadores em paralelo como o número de unidades de pesquisa no seu serviço. Na **Escala de Definições,**  >  aumente as [réplicas](search-capacity-planning.md) ou divisórias para processamento paralelo: uma réplica adicional ou partição para cada carga de trabalho indexante. Deixe um número suficiente para o volume de consulta existente. Sacrificar cargas de trabalho de consulta para indexação não é uma boa troca.

3. Distribua dados em vários recipientes a um nível que os indexantes de Pesquisa Cognitiva Azure possam alcançar. Isto pode ser várias tabelas na Base de Dados Azure SQL, vários contentores no armazenamento de Azure Blob, ou várias coleções. Defina um objeto de origem de dados para cada tabela ou recipiente.

4. Criar e agendar vários indexadores para correr em paralelo:

   + Assuma um serviço com seis réplicas. Configure seis indexantes, cada um mapeado para uma fonte de dados contendo um sexto do conjunto de dados para uma divisão de 6 vias de todo o conjunto de dados. 

   + Aponte cada indexante para o mesmo índice. Para cargas de trabalho de pesquisa cognitiva, aponte cada indexante para o mesmo skillset.

   + Dentro de cada definição indexante, marque o mesmo padrão de execução em tempo de execução. Por exemplo, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` cria um horário para 2018-05-15 em todos os indexantes, funcionando em intervalos de oito horas.

Na hora programada, todos os indexantes iniciam a execução, carregando dados, aplicando enriquecimentos (se configurar um pipeline de pesquisa cognitiva) e escrever para o índice. A Azure Cognitive Search não bloqueia o índice para atualizações. As escritas simultâneas são geridas, com repetição se uma determinada escrita não for bem sucedida na primeira tentativa.

> [!Note]
> Ao aumentar as réplicas, considere aumentar a contagem de partição se o tamanho do índice for projetado para aumentar significativamente. As divisórias armazenam fatias de conteúdo indexado; quanto mais divisórias tiveres, menor será a fatia que cada um tem para armazenar.

## <a name="see-also"></a>Ver também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Indexação no portal](search-import-data-portal.md)
+ [Indexante de base de dados Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador da BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança em Pesquisa Cognitiva Azure](search-security-overview.md)