---
title: Introdução à análise operacional interna no Azure Cosmos DB com Apache Spark
description: Saiba como você pode usar o suporte interno para Apache Spark no Azure Cosmos DB para executar análises operacionais e ia
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 952dd084bccd67a0a8833002d73d3aaf1d5dfb25
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338615"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Análise operacional interna em Azure Cosmos DB com Apache Spark

Com Azure Cosmos DB, você pode executar análises distribuídas globalmente e de baixa latência e ia em dados transacionais. Com suporte nativo para notebooks Apache Spark e Jupyter, Azure Cosmos DB ajuda a reduzir o tempo de insights. Como os dados são ingeridos, atendidos e as análises são executadas na réplica de banco de dados local em uma região do Azure. Você pode executar diretamente Apache Spark consultas em dados de vários modelos indexados armazenados em suas partições de dados.

O suporte interno para Apache Spark no Azure Cosmos DB permite que você execute a análise de Apache Spark em seus dados armazenados em uma conta do Azure Cosmos. Ele fornece o suporte nativo para trabalhos de Apache Spark a serem executados diretamente em seus bancos de dados Cosmos distribuídos globalmente. Com esses recursos, os desenvolvedores, os engenheiros de dados e os cientistas de dados podem usar Azure Cosmos DB como uma plataforma de dados flexível, escalonável e de alto desempenho para executar cargas de trabalho **OLTP e OLAP/HTAP** .

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

### <a name="low-latency-operational-analytics-and-ai"></a>Análise operacional de baixa latência e ia

Com Apache Spark no banco de dados do Azure Cosmos distribuído globalmente, agora você pode obter um rápido tempo de insight em todo o mundo. O Azure Cosmos DB permite **análise operacional de baixa latência globalmente distribuída** em escala elástica com três técnicas-chave:

* Como o banco de dados de Cosmos do Azure é distribuído globalmente, todos eles são ingeridos localmente, onde os produtores dos dados (por exemplo, usuários) estão localizados. As consultas são servidas em relação às réplicas locais mais próximas aos produtores e aos consumidores de dados, independentemente de onde estão localizadas no mundo.

* Todas as suas consultas analíticas são executadas diretamente nos dados indexados armazenados dentro das partições de dados sem a necessidade de nenhuma movimentação de dados desnecessária.

* Como o Spark é colocado com Azure Cosmos DB, menos traduções intermediárias e movimentações de dados ocorrem, resultando em um melhor desempenho e escalabilidade.

* Todos os Azure Cosmos DB principais recursos como vários mestres, failover automático, Zonas de Disponibilidade, etc. estão disponíveis para Apache Spark internas no Azure Cosmos DB.

### <a name="unified-serverless-experience-for-apache-spark"></a>Experiência unificada sem servidor para Apache Spark

Como um banco de dados multimodelo, o Azure Cosmos DB agora expande seu suporte para APIs de OSS fornecendo uma **experiência unificada sem servidor para Apache Spark** com modelos de dados de chave-valor, documento, gráfico e família de colunas. Modelos de dados diferentes têm suporte usando o MongoDB, Cassandra, Gremlin, etcd e APIs do SQL-todos estão operando nos mesmos dados subjacentes. 

Com o suporte do Apache Spark no Azure Cosmos DB, você pode dar suporte nativo a aplicativos escritos em escala, Python, Java e usar várias bibliotecas fortemente integradas para SQL. Essas bibliotecas incluem ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark [MLlib](https://spark.apache.org/mllib/)), processamento de fluxo ([streaming estruturado do Spark](https://spark.apache.org/streaming/)) e processamento de grafo (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Essas ferramentas facilitam o uso do Apache Spark para uma variedade de casos de uso. Você não precisa lidar com o gerenciamento de clusters Spark ou Spark. Você pode usar as APIs de Apache Spark familiares e os **notebooks Jupyter** para análise e API do SQL ou qualquer uma das APIs NOSQL de OSS, como Cassandra para processamento transacional nos mesmos dados subjacentes ao mesmo tempo.

### <a name="no-schema-or-index-management"></a>Nenhum gerenciamento de esquema ou índice

Ao contrário dos bancos de dados analíticos tradicionais, com Azure Cosmos DB, engenheiros de dados e cientistas de data não precisam mais lidar com o gerenciamento complicado de esquema e índice. O mecanismo de banco de dados no Azure Cosmos DB não requer nenhum gerenciamento explícito de esquema ou índice e é capaz de indexar automaticamente todos os dados que ele ingere para atender às consultas de Apache Spark rapidamente.

### <a name="consistency-choices"></a>Opções de consistência

Uma vez que os trabalhos de Apache Spark são executados nas partições de dados do seu Azure Cosmos, as consultas receberão as [cinco opções de consistência bem definidas](consistency-levels.md). Esses modelos de consistência oferecem a flexibilidade de escolher uma consistência estrita para fornecer os resultados mais precisos para algoritmos de aprendizado de máquina sem comprometer a latência e a alta disponibilidade.

### <a name="comprehensive-slas"></a>SLAs abrangentes

Os trabalhos de Apache Spark terão os benefícios de Azure Cosmos DB como [SLAs](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) abrangentes líderes do setor (99,999) sem nenhuma sobrecarga de gerenciamento de clusters de Apache Spark separados. Esses SLAs abrangem a taxa de transferência, a latência no 99 º percentil, a consistência e a alta disponibilidade. 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>Cargas de trabalho analíticas e operacionais mistas com isolamento completo

A integração do Apache Spark ao Azure Cosmos DB preenche a separação transacional e analítica, que tem sido um dos principais pontos problemáticos do cliente ao criar aplicativos nativos de nuvem em escala global. As cargas de trabalho OLTP e OLAP são executadas lado a lado e não interferem entre si.

### <a name="low-latency-analytical-and-transactional-storage"></a>Armazenamento analítico e transacional de baixa latência

Azure Cosmos DB armazena nativamente os dados em duas camadas de armazenamento distintos: armazenamento analítico e transacional (orientado por linha) (orientado por coluna, no formato de arquivo Apache parquet). Ele replica os dados em cada camada globalmente e permite que os usuários gerenciem dados de forma independente nessas camadas com base nas políticas de retenção.

Essa arquitetura de armazenamento permite cargas de trabalho transacionais e analíticas de missão crítica nos mesmos dados distribuídos globalmente. Com o Cosmos DB, você não precisa de nenhuma operação de ETL ou executar qualquer movimentação de dados desnecessária. Você pode simplesmente executar cargas de trabalho transacionais e analíticas nos mesmos dados subjacentes. As cargas de trabalho transacionais podem usar as APIs de acesso transacionais familiares, incluindo SQL, Cassandra, MongoDB, Gremlin e etcd. Enquanto as cargas de trabalho de análise e ia podem usar o Apache Spark interno do SQL, ML frameworks e todo o Apache Spark ferramentas.

### <a name="snapshots-and-historical-analytical-queries"></a>Instantâneos e consultas analíticas históricas

Você pode criar instantâneos sob demanda ou na agenda dos dados compactados colunares armazenados na camada analítica para executar consultas analíticas diretamente em um instantâneo específico. Esse recurso permite que os flashback ou consultas analíticas de viagem de tempo, reversões, trilhas de auditoria históricas totais e retenham experimentos de inteligência de máquina e de ia.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os benefícios do Azure Cosmos DB, consulte o artigo [visão geral](introduction.md) .
* [Introdução à API de Azure Cosmos DB para MongoDB](mongodb-introduction.md)
* [Introdução ao Azure Cosmos DB API do Cassandra](cassandra-introduction.md)
* [Introdução à API do Azure Cosmos DB Gremlin](graph-introduction.md)
* [Introdução ao Azure Cosmos DB API de Tabela](table-introduction.md)