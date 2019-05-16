---
title: Introdução à análise operacional integrada no Azure Cosmos DB com o Apache Spark
description: Saiba como pode utilizar o suporte incorporado para o Apache Spark no Azure Cosmos DB para executar análises operacionais e de IA
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/10/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: c62639feed7ced9d92e29715e350b952465a94a1
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517728"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Análise operacional incorporada no Azure Cosmos DB com o Apache Spark (pré-visualização) 

O suporte incorporado para o Apache Spark no Azure Cosmos DB permite-lhe executar análises do Apache Spark relativamente aos seus dados armazenados numa conta do Cosmos do Azure. Ele fornece o suporte nativo para tarefas do Apache Spark executar diretamente em seus bancos de dados globalmente distribuídos do Cosmos. Com estas capacidades, os desenvolvedores, engenheiros de dados e os cientistas de dados podem utilizar do Azure Cosmos DB como um flexível, dimensionável e plataforma de dados de elevado desempenho para executar os dois **OLTP e OLAP/HTAP** cargas de trabalho. 

Spark de computação está automaticamente disponível em todas as regiões do Azure associado à sua conta do Cosmos do Azure. Tarefas do Spark utilizam a capacidade de vários mestre do Azure Cosmos DB e pode escrever ou uma consulta em relação as réplicas locais em cada região. 

> [!NOTE]
> O suporte incorporado para o Apache Spark no Azure Cosmos DB está atualmente em pré-visualização limitada. Para se inscrever na pré-visualização, navegue até [Inscreva-se na pré-visualização](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB) página. 

O suporte do Apache Spark no Azure Cosmos DB oferece as seguintes vantagens:

* Pode obter o tempo mais rápido para obter conhecimentos aprofundados para os utilizadores distribuídos geograficamente e os dados.

* Pode simplificar a arquitetura da sua solução e inferior a [Custo Total de propriedade](total-cost-ownership.md) (TCO). O sistema terá o menor número de componentes de processamento de dados e evita qualquer movimento de dados desnecessária entre eles.

* Cria um [security](secure-access-to-data.md), [conformidade](compliance.md)e o limite de auditoria que abrange todos os dados sob gestão.

* Fornece "sempre ativa" ou [elevada disponibilidade](high-availability.md) análises de utilizador final que são apoiados por SLAs rigorosas.

![Suporte para Apache Spark na visualização do Azure Cosmos DB](./media/spark-api-introduction/spark-api-visualization.png)
 
Usando o suporte do Apache Spark no Azure Cosmos DB, pode criar e implementar soluções de IA e aprendizagem, modelos, Análise Preditiva, recomendações, IoT, o cliente 360, deteção de fraudes, análises clickstream de sentimentos de texto. Essas soluções funcionam diretamente nos seus dados do Azure Cosmos DB.

Pode configurar o batch e transmissão em fluxo de trabalho ETL no Azure Cosmos DB, sem ter de sair do serviço de base de dados ou adicionar mais serviços de computação. Pode dimensionar o ambiente de computação quando tiver de executar a tarefa ETL e dimensioná-lo a voltar a reduzir quando a tarefa está concluída.

O suporte do Apache Spark no Azure Cosmos DB oferece suporte incorporado de Machine Learning nos tempos de execução do Apache Spark. Os tempos de execução incluem Spark MLLib, Microsoft Machine Learning para Spark, Azure Machine Learning e os serviços cognitivos. Com esses recursos, os cientistas de dados, engenheiros de dados e analistas de dados podem criar e operacionalizar modelos de aprendizagem automática diretamente no Azure Cosmos DB, numa fração de tempo e com baixo custo.


## <a name="key-benefits"></a>Principais vantagens

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Análise operacional de distribuição global de baixa latência e a IA

Com o Apache Spark na base de dados globalmente distribuído do Cosmos do Azure, agora pode obter tudo de um tempo rápido de resposta em todo o mundo. O Azure Cosmos DB permite **análise operacional de distribuição global de baixa latência** em escala elástica com três técnicas principais:

* Uma vez que a sua base de dados do Cosmos do Azure é distribuído globalmente, todos os dados são ingeridos localmente onde estão localizados os produtores de dados (por exemplo, utilizadores). As consultas são servidas contra as réplicas locais mais próximos para os produtores e consumidores de dados, independentemente de onde estão localizados no mundo. 

* Todas as consultas de análise são executadas diretamente nos dados indexados armazenados no interior das partições de dados sem a necessidade de qualquer movimento de dados desnecessários. 

* Como o Spark é colocalizado com o Azure Cosmos DB, menos traduções intermediárias e movimentos de dados ocorrem, resultando num melhor desempenho e escalabilidade.

### <a name="unified-serverless-experience-for-apache-spark"></a>Experiência sem servidor unificada para o Apache Spark

Como uma base de dados com múltiplos modelo, Azure Cosmos DB agora se expande o suporte de OSS APIs, fornecendo uma **experiência sem servidor para o Apache Spark unificada** com chave-valor, documentos, gráfico, modelos de dados de famílias de coluna. Vários modelos de dados são suportados com o MongoDB, Cassandra, Gremlin, Etcd e APIs de SQL - operando todos os mesmos dados subjacentes. 

Com o suporte do Apache Spark no Azure Cosmos DB, nativamente pode oferecer suporte a aplicativos escritos em Scala, Python, Java e utilizar várias bibliotecas estreitamente integradas para SQL. Estas bibliotecas incluem ([Spark SQL](https://spark.apache.org/sql/)), de machine learning (Spark [MLlib](https://spark.apache.org/mllib/)), processamento de fluxo ([Spark Structured Streaming](https://spark.apache.org/streaming/)) e o processamento de gráficos (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Essas ferramentas facilitam a tirar partido do Apache Spark para uma variedade de casos de utilização. Não precisa lidar com gestão de Spark ou clusters do Spark. Pode utilizar as familiares APIs de Spark do Apache e **blocos de notas do Jupyter** para análise e a API de SQL ou a qualquer uma das APIs OSS NoSQL como Cassandra para processamento transacional nos mesmos dados subjacentes ao mesmo tempo.

### <a name="no-schema-or-index-management"></a>Sem gestão de índices ou esquemas

Ao contrário o bases de dados de análises tradicionais, com o Azure Cosmos DB, os engenheiros de dados e os cientistas de dados já não precisam lidar com esquema complicada e gestão de índices. O motor de base de dados no Azure Cosmos DB não requer qualquer gerenciamento explícito de índices ou esquemas e é capaz de indexar automaticamente todos os dados que ingere para servir as consultas do Apache Spark rapidamente. 

### <a name="consistency-choices"></a>Escolhas de consistência

Uma vez que as tarefas do Apache Spark são executadas nas partições de dados da sua base de dados do Cosmos do Azure, obterá as consultas a [cinco escolhas de consistência bem definidos](consistency-levels.md). Estes modelos de consistência oferecem a flexibilidade de escolher uma consistência rigorosa para fornecer os resultados mais precisos para algoritmos de machine learning sem comprometer a latência e elevada disponibilidade. 

### <a name="slas"></a>SLAs

As tarefas do Apache Spark terá os benefícios do Azure Cosmos DB, como líder abrangente da indústria [SLAs](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) sem qualquer sobrecarga de gerenciamento de clusters do Apache Spark separados.... Esses SLAs abrangem o débito, latência no percentil de 99, a consistência e a elevada disponibilidade. 

### <a name="mixed-workloads"></a>Cargas de trabalho mistas

A integração do Apache Spark no Azure Cosmos DB pontes a separação transacional e análise, que tem sido um dos pontos de tensão de principais de cliente ao criar aplicações nativas da cloud à escala global. 

## <a name="built-in-jupyter-notebooks-support"></a>Suporte interno a blocos de notas Jupyter

O Azure Cosmos DB suporta blocos de notas do Jupyter para todas as APIs, como Cassandra, MongoDB, SQL, Gremlin e tabela. Os blocos de notas do Jupyter execute dentro as contas do Azure Cosmos DB e eles aprimoram a experiência do desenvolvedor. O suporte de bloco de notas incorporados para todos os modelos de dados e APIs do Azure Cosmos DB permite-lhe executar interativamente consultas. Também pode executar modelos de aprendizagem automática e analisar os dados armazenados em seus bancos de dados do Cosmos do Azure. Ao utilizar a experiência de bloco de notas do Jupyter, pode analisar os dados armazenados, criar e formar modelos do machine learning e efetuar inferência nos dados no portal do Azure, conforme mostrado na imagem seguinte:

![Suporte de blocos de notas do Jupyter no Azure Cosmos DB](./media/spark-api-introduction/jupyter-notebooks-portal.png)

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre os benefícios do Azure Cosmos DB, veja a [descrição geral](introduction.md) artigo.
* [Introdução à API do Azure Cosmos DB para MongoDB](mongodb-introduction.md)
* [Introdução à API Cassandra do Azure Cosmos DB](cassandra-introduction.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](graph-introduction.md)
* [Introdução à API de Tabela do Azure Cosmos DB](table-introduction.md)




