---
title: O que é Apache HBase em Azure HDInsight?
description: Uma introdução ao Apache HBase no HDInsight, uma base de dados NoSQL baseada no Hadoop. Saiba mais sobre casos de utilização e compare o HBase a outros clusters do Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/03/2020
ms.openlocfilehash: 97814f4d22629fd74f395887a7361a3aabe55012
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271841"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>O que é Apache HBase em Azure HDInsight

[Apache HBase](https://hbase.apache.org/) é uma base de dados noSQL de código aberto que é construída em [Apache Hadoop](https://hadoop.apache.org/) e modelada após [o Google BigTable](https://cloud.google.com/bigtable/). O HBase fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semiestruturados numa base de dados sem esquema, organizada por famílias de colunas.

Do ponto de vista do utilizador, o HBase é semelhante a uma base de dados. Os dados são armazenados nas linhas e colunas de uma tabela, e os dados dentro de uma linha são agrupados pela família das colunas. O HBase é uma base de dados sem esquema uma vez que não é preciso definir as colunas nem os tipos de dados nelas armazenados antes de os utilizar. O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. Pode depender da redundância de dados, do processamento em lotes e de outras funcionalidades fornecidas por aplicações distribuídas do ecossistema do Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Como é implementada a Apache HBase no Azure HDInsight?

O HBase do HDInsight é fornecido como um cluster gerido que está integrado no ambiente do Azure. Os clusters estão configurados para armazenar dados diretamente no [Armazenamento Azure,](./../hdinsight-hadoop-use-blob-storage.md)que proporciona baixa latência e maior elasticidade no desempenho e nas escolhas de custos. Isto permite aos clientes criar sites interativos que funcionam com grandes conjuntos de dados, criar serviços que armazenam dados telemétricos e de sensores de milhões de pontos finais e analisar esses dados com tarefas do Hadoop. O HBase e o Hadoop são bons pontos de partida para projetos de macrodados no Azure; em particular, podem permitir que as aplicações em tempo real trabalhem com grandes conjuntos de dados.

A implementação do HDInsight tira partido da arquitetura de escalamento horizontal do HBase para fornecer fragmentação automática de tabelas, consistência forte para operações de leitura e escrita e ativação pós-falha automática. O desempenho é melhorado graças à colocação em cache dentro da memória para as operações de leitura e à transmissão em fluxo de alto débito para as operações de escrita. O cluster HBase pode ser criado no interior da rede virtual. Para obter mais detalhes, veja [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Criar clusters do HDInsight na Rede Virtual do Azure).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Como são geridos os dados no HBase do HDInsight?

É possível gerir os dados no HBase ao utilizar os comandos `create`, `get`, `put` e `scan` da shell do HBase. Os dados são escritos na base de dados através do comando `put` e lidos através do comando `get`. O comando `scan` é utilizado para obter dados de várias linhas numa tabela. É possível ainda gerir os dados através da API C# do HBase, que fornece uma biblioteca de cliente sobre a API REST do HBase. Uma base de dados HBase também pode ser consultada utilizando [a Hive Apache](https://hive.apache.org/). Para uma introdução a estes modelos de programação, consulte [Get started using Apache HBase with Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md). Também estão disponíveis coprocessadores, que permitem o processamento de dados nos nós que acolhem a base de dados.

> [!NOTE]  
> O thrift não é suportado pelo HBase no HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Utilize casos para Apache HBase

O caso canónico de utilização para o qual o BigTable (e por extensão, HBase) foi criado a partir da pesquisa web. Os motores de busca criam índices que mapeiam termos para as páginas Web que os contêm. No entanto, o HBase é adequado para muitos outros casos de utilização, sendo que vários deles estão descritos nesta secção.

|Cenário |Descrição |
|---|---|
|Arquivo de chave-valor|O HBase pode ser usado como uma loja de valor-chave, e é adequado para gerir sistemas de mensagens. O Facebook utiliza hbase para o seu sistema de mensagens, e é ideal para armazenar e gerir comunicações de Internet. O WebTable utiliza o HBase para procurar e gerir tabelas extraídas de páginas Web.|
|Dados de sensores|O HBase é útil para capturar os dados que são recolhidos de forma incremental a partir de várias origens. Isto inclui análise social, séries temporais, manter os dashboards interativos atualizados com tendências e contadores, e gerir sistemas de registo de auditoria. Os exemplos incluem o terminal comercial Bloomberg e o OpenTSDB (Open Time Series Database), que armazena e fornece acesso a métricas recolhidas sobre o estado de funcionamento dos sistemas de servidor.|
|Consulta em tempo real|[Apache Phoenix](https://phoenix.apache.org/) é um motor de consulta SQL para Apache HBase. É acedido como um controlador JDBC, e permite consultar e gerir tabelas HBase usando SQL.|
|HBase como uma plataforma|As aplicações podem ser executadas sobre o HBase ao utilizá-lo como um arquivo de dados. Exemplos incluem Phoenix, [OpenTSDB,](http://opentsdb.net/)Kiji e Titan. As aplicações também podem ser integradas no HBase. Exemplos incluem [Apache Hive,](https://hive.apache.org/) [Apache Pig,](https://pig.apache.org/) [Solr,](https://lucene.apache.org/solr/) [Apache Storm,](https://storm.apache.org/) [Apache Flume,](https://flume.apache.org/) [Apache Impala,](https://impala.apache.org/) [Apache Spark,](https://spark.apache.org/) [Gânglios](http://ganglia.info/)e [Apache Drill.](https://drill.apache.org/)|

## <a name="next-steps"></a>Passos seguintes

* [Começar a usar Apache HBase com Apache Hadoop em HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Criar clusters do HDInsight na Rede Virtual do Azure)
* [Configure a replicação de Apache HBase no HDInsight](apache-hbase-replication.md)
