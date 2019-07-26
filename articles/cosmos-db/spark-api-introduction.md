---
title: Introdução à análise operacional interna no Azure Cosmos DB com Apache Spark
description: Saiba como você pode usar o suporte interno para Apache Spark no Azure Cosmos DB para executar análises operacionais e ia
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/23/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 245df0632765c4000bdf5da3e428187d2b068866
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402068"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Análise operacional interna em Azure Cosmos DB com Apache Spark (versão prévia) 

O suporte interno para Apache Spark no Azure Cosmos DB permite que você execute a análise de Apache Spark em seus dados armazenados em uma conta do Azure Cosmos. Ele fornece o suporte nativo para trabalhos de Apache Spark a serem executados diretamente em seus bancos de dados Cosmos distribuídos globalmente. Com esses recursos, os desenvolvedores, os engenheiros de dados e os cientistas de dados podem usar Azure Cosmos DB como uma plataforma de dados flexível, escalonável e de alto desempenho para executar cargas de trabalho **OLTP e OLAP/HTAP** . 

A computação do Spark está disponível automaticamente em todas as regiões do Azure associadas à sua conta do Azure Cosmos. Os trabalhos do Spark usam o recurso de vários mestres do Azure Cosmos DB e podem gravar ou consultar as réplicas locais em cada região. 

> [!NOTE]
> O suporte interno para Apache Spark no Azure Cosmos DB está atualmente em versão prévia limitada. Para se inscrever para a versão prévia, navegue para inscrever-se na [página de visualização](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB). 

O suporte do Apache Spark no Azure Cosmos DB oferece os seguintes benefícios:

* Você pode obter o tempo mais rápido para insights para usuários e dados distribuídos geograficamente.

* Você pode simplificar a arquitetura da sua solução e reduzir o [custo total de propriedade](total-cost-ownership.md) (TCO). O sistema terá o número mínimo de componentes de processamento de dados e evitará qualquer movimentação de dados desnecessária entre eles.

* Cria um limite de [segurança](secure-access-to-data.md), [conformidade](compliance.md)e auditoria que abrange todos os dados sob gerenciamento.

* Fornece análise de usuário final "sempre ligado" ou [altamente disponível](high-availability.md) que são apoiadas por SLAs rigorosos.

![Suporte Apache Spark na visualização Azure Cosmos DB](./media/spark-api-introduction/spark-api-visualization.png)
 
Usando o suporte a Apache Spark no Azure Cosmos DB, você pode criar e implantar soluções como ia e modelos de aprendizado profundo, análise preditiva, recomendações, IoT, cliente 360, detecção de fraudes, cliques de texto, análise de inscrições. Essas soluções funcionam diretamente com seus dados de Azure Cosmos DB.

Você pode configurar o trabalho ETL de fluxo e de lote em Azure Cosmos DB, sem precisar ir fora do serviço de banco de dados ou adicionar serviços de computação adicionais. Você pode dimensionar de forma elástica o ambiente de computação quando precisar executar o trabalho ETL e dimensioná-lo novamente quando o trabalho for concluído.

O suporte a Apache Spark no Azure Cosmos DB oferece suporte Machine Learning interno nos tempos de execução do Apache Spark. Os tempos de execução incluem Spark MLLib, Microsoft Machine Learning para Spark, Azure Machine Learning e serviços cognitivas. Com esses recursos, os cientistas de dados, os engenheiros de dados e os analistas de dados podem criar e colocar em operação os modelos de aprendizado de máquina diretamente dentro de Azure Cosmos DB, em uma fração de tempo e com o baixo custo.


## <a name="key-benefits"></a>Principais vantagens

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globalmente distribuído, análise operacional de baixa latência e ia

Com Apache Spark no banco de dados do Azure Cosmos distribuído globalmente, agora você pode obter um rápido tempo de insight em todo o mundo. O Azure Cosmos DB permite **análise operacional de baixa latência globalmente distribuída** em escala elástica com três técnicas-chave:

* Como o banco de dados de Cosmos do Azure é distribuído globalmente, todos eles são ingeridos localmente, onde os produtores dos dados (por exemplo, usuários) estão localizados. As consultas são servidas em relação às réplicas locais mais próximas aos produtores e aos consumidores de dados, independentemente de onde estão localizadas no mundo. 

* Todas as suas consultas analíticas são executadas diretamente nos dados indexados armazenados dentro das partições de dados sem a necessidade de nenhuma movimentação de dados desnecessária. 

* Como o Spark é colocado com Azure Cosmos DB, menos traduções intermediárias e movimentações de dados ocorrem, resultando em um melhor desempenho e escalabilidade.

### <a name="unified-serverless-experience-for-apache-spark"></a>Experiência unificada sem servidor para Apache Spark

Como um banco de dados multimodelo, o Azure Cosmos DB agora expande seu suporte para APIs de OSS fornecendo uma experiência unificada sem **servidor para Apache Spark** com modelos de dados de chave-valor, documento, gráfico e família de colunas. Modelos de dados diferentes têm suporte usando o MongoDB, Cassandra, Gremlin, etcd e APIs do SQL-todos estão operando nos mesmos dados subjacentes. 

Com o suporte do Apache Spark no Azure Cosmos DB, você pode dar suporte nativo a aplicativos escritos em escala, Python, Java e usar várias bibliotecas fortemente integradas para SQL. Essas bibliotecas incluem ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark [MLlib](https://spark.apache.org/mllib/)), processamento de fluxo ([streaming estruturado do Spark](https://spark.apache.org/streaming/)) e processamento de grafo (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Essas ferramentas facilitam o uso do Apache Spark para uma variedade de casos de uso. Você não precisa lidar com o gerenciamento de clusters Spark ou Spark. Você pode usar as APIs de Apache Spark familiares e os **notebooks Jupyter** para análise e API do SQL ou qualquer uma das APIs NOSQL de OSS, como Cassandra para processamento transacional nos mesmos dados subjacentes ao mesmo tempo.

### <a name="no-schema-or-index-management"></a>Nenhum gerenciamento de esquema ou índice

Ao contrário dos bancos de dados analíticos tradicionais, com Azure Cosmos DB, engenheiros de dados e cientistas de data não precisam mais lidar com o gerenciamento complicado de esquema e índice. O mecanismo de banco de dados no Azure Cosmos DB não requer nenhum gerenciamento explícito de esquema ou índice e é capaz de indexar automaticamente todos os dados que ele ingere para atender às consultas de Apache Spark rapidamente. 

### <a name="consistency-choices"></a>Opções de consistência

Uma vez que os trabalhos de Apache Spark são executados nas partições de dados do seu Azure Cosmos, as consultas receberão as [cinco opções de consistência bem definidas](consistency-levels.md). Esses modelos de consistência oferecem a flexibilidade de escolher uma consistência estrita para fornecer os resultados mais precisos para algoritmos de aprendizado de máquina sem comprometer a latência e a alta disponibilidade. 

### <a name="comprehensive-slas"></a>SLAs abrangentes

Os trabalhos de Apache Spark terão os benefícios de Azure Cosmos DB como [SLAs](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) abrangentes líderes do setor (99,999) sem nenhuma sobrecarga de gerenciamento de clusters de Apache Spark separados. Esses SLAs abrangem a taxa de transferência, a latência no 99 º percentil, a consistência e a alta disponibilidade. 

### <a name="mixed-workloads"></a>Cargas de trabalho mistas

A integração do Apache Spark ao Azure Cosmos DB preenche a separação transacional e analítica, que tem sido um dos principais pontos problemáticos do cliente ao criar aplicativos nativos de nuvem em escala global. 

## <a name="scenarios-for-azure-cosmos-db-spark-support"></a>Cenários para suporte Azure Cosmos DB Spark

### <a name="retail-and-consumer-goods"></a>Bens de varejo e de consumo

Você pode usar o suporte do Spark no Azure Cosmos DB para fornecer recomendações e ofertas em tempo real. Você pode ajudar os clientes a descobrir os itens necessários com personalização em tempo real e recomendações de produtos.

* Você pode usar o suporte interno de Machine Learning fornecido pelo Apache Spark Runtime para gerar recomendações em tempo real em todos os catálogos de produtos.

* Você pode clicar em transmitir dados, comprar dados e dados do cliente para fornecer recomendações direcionadas que conduzem o valor de tempo de vida.

* Usando o recurso de distribuição global do Azure Cosmos DB, grandes volumes de dados do produto que são distribuídos entre regiões podem ser analisados em milissegundos.

* Você pode obter informações rapidamente para usuários e dados distribuídos geograficamente. Você pode melhorar a taxa de conversão de promoção servindo o anúncio correto para o usuário correto no momento certo.

* Você pode aproveitar o recurso de streaming do Spark embutido para enriquecer dados dinâmicos, combinando-os com dados estáticos do cliente. Dessa forma, você pode fornecer anúncios mais personalizados e direcionados em tempo real e em contexto com o que os clientes estão fazendo.

A imagem a seguir mostra como Azure Cosmos DB suporte do Spark é usado para otimizar o preço e as promoções:

![Suporte ao Spark do Azure Cosmos DB para otimizar o preço e as promoções](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


A imagem a seguir mostra como Azure Cosmos DB suporte do Spark é usado no mecanismo de recomendação em tempo real:

![Suporte ao Azure Cosmos DB Spark no mecanismo de recomendação em tempo real](./media/spark-api-introduction/real-time-recommendation-engine.png)

### <a name="manufacturing-and-iot"></a>Manufatura e IoT

A plataforma de análise interna do Azure Cosmos DB permite que você habilite a análise em tempo real de dados de IoT de milhões de dispositivos em escala global. Você pode fazer inovações modernas como prever padrões meteorológicos, análise preditiva e otimizações de energia.

* Usando Azure Cosmos DB, você pode minar dados como métricas de ativos em tempo real e fatores meteorológicos e, em seguida, aplicar análise de grade inteligente para otimizar o desempenho de dispositivos conectados no campo. O Smart Grid Analytics é a chave para controlar os custos operacionais, melhorar a confiabilidade da grade e fornecer serviços de energia personalizados aos consumidores.

A imagem a seguir mostra como o suporte ao Spark do Azure Cosmos DB é usado para ler métricas de dispositivos IoT e aplicar análise de grade inteligente:

![Suporte do Spark do Azure Cosmos DB para ler métricas de dispositivos IoT](./media/spark-api-introduction/read-metrics-from-iot-devices.png)


### <a name="predictive-maintenance"></a>Manutenção preventiva

* A manutenção de ativos, como compactadores, que são usados em pequenas Rigs de análise para plataformas de água profunda, é um empreendimento complexo. Esses ativos estão localizados em todo o mundo e geram petabytes de dados. Usando Azure Cosmos DB, você pode criar um pipeline de dados de previsão de ponta a ponta que usa o streaming do Spark para processar grandes quantidades de telemetria de sensor, armazenar partes de ativos e dados de mapeamentos de sensor.

* Você pode criar e implantar modelos de aprendizado de máquina para prever falhas de ativo antes que elas ocorram e emitir as ordens de trabalho de manutenção antes que a falha ocorra.

A imagem a seguir mostra como o suporte ao Spark do Azure Cosmos DB é usado para criar um sistema de manutenção preditiva:

![O suporte ao Spark do Azure Cosmos DB para criar um sistema de manutenção preditiva](./media/spark-api-introduction/predictive-maintenance-system.png)

A imagem a seguir mostra como o suporte ao Spark do Azure Cosmos DB é usado para criar um sistema de diagnóstico de veículo em tempo real:

![O suporte ao Spark do Azure Cosmos DB para criar um sistema de diagnóstico de veículo em tempo real](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

### <a name="gaming"></a>Jogos

* Com o suporte interno ao Spark, o Azure Cosmos DB permite que você crie, dimensione e implante facilmente modelos de análise avançada e de aprendizado de máquina em minutos para criar a melhor experiência de jogos possível.

* Você pode analisar o Player, a compra e os dados comportamentais para criar ofertas personalizadas relevantes para obter altas taxas de conversão.

* Usando o Spark Machine Learning, você pode analisar e obter informações sobre os dados de telemetria do jogo. Você pode diagnosticar e impedir tempos de carregamento lentos e problemas no jogo.

A imagem a seguir mostra como o suporte ao Spark do Azure Cosmos DB é usado no Game Analytics:

![Suporte ao Spark do Azure Cosmos DB no Game Analytics](./media/spark-api-introduction/gaming-analytics.png)

## <a name="built-in-jupyter-notebooks-support"></a>Suporte interno para notebooks Jupyter

O Azure Cosmos DB dá suporte a blocos de anotações Jupyter internos para todas as APIs, como Cassandra, MongoDB, SQL, Gremlin e tabela. Os notebooks Jupyter são executados nas contas do Azure Cosmos e aprimoram a experiência do desenvolvedor. O suporte ao bloco de anotações interno para todos os Azure Cosmos DB APIs e modelos de dados permite executar consultas interativamente. Você também pode executar modelos de aprendizado de máquina e analisar os dados armazenados nos bancos de dado do Azure Cosmos. Usando a experiência do Jupyter notebook, você pode analisar os dados armazenados, criar e treinar modelos de aprendizado de máquina e executar inferência nos dados na portal do Azure, conforme mostrado na imagem a seguir:

![Suporte a notebooks Jupyter no Azure Cosmos DB](./media/spark-api-introduction/jupyter-notebooks-portal.png)

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre os benefícios do Azure Cosmos DB, consulte o artigo [visão geral](introduction.md) .
* [Introdução à API de Azure Cosmos DB para MongoDB](mongodb-introduction.md)
* [Introdução ao Azure Cosmos DB API do Cassandra](cassandra-introduction.md)
* [Introdução à API do Azure Cosmos DB Gremlin](graph-introduction.md)
* [Introdução ao Azure Cosmos DB API de Tabela](table-introduction.md)




