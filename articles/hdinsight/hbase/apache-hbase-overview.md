---
title: O que é Apache HBase em Azure HDInsight?
description: Uma introdução ao Apache HBase no HDInsight, uma base de dados NoSQL baseada no Hadoop. Saiba mais sobre casos de utilização e compare o HBase a outros clusters do Hadoop.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 98872cc1315d946c63825318d2b98460031e128a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942905"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>O que é Apache HBase em Azure HDInsight

[Apache HBase](https://hbase.apache.org/) é uma base de dados NoSQL de código aberto que é construída em Apache Hadoop e modelada após [o Google BigTable](https://cloud.google.com/bigtable/). O HBase fornece acesso aleatório e forte consistência para grandes quantidades de dados numa base de dados sem esquemas. A base de dados é organizada por famílias de colunas.

Do ponto de vista do utilizador, o HBase é semelhante a uma base de dados. Os dados são armazenados nas linhas e colunas de uma tabela, e os dados dentro de uma linha são agrupados pela família das colunas. HBase é uma base de dados sem esquemas. As colunas e os tipos de dados podem ser indefinidos antes de os utilizarem. O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. Pode contar com redundância de dados, processamento de lotes e outras funcionalidades que são fornecidas por aplicações distribuídas no ambiente Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Como é implementado o Apache HBase no Azure HDInsight?

O HBase do HDInsight é fornecido como um cluster gerido que está integrado no ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no [Azure Storage,](./../hdinsight-hadoop-use-blob-storage.md)que proporciona baixa latência e aumento da elasticidade nas escolhas de desempenho e custo. Esta propriedade permite que os clientes construam websites interativos que funcionam com grandes conjuntos de dados. Para construir serviços que armazenam sensores e dados de telemetria a partir de milhões de pontos finais. E para analisar estes dados com empregos hadoop. HBase e Hadoop são bons pontos de partida para o grande projeto de dados em Azure. Os serviços podem permitir que aplicações em tempo real funcionem com grandes conjuntos de dados.

A implementação HDInsight utiliza a arquitetura de escala-out da HBase para fornecer o fragmento automático das tabelas. E forte consistência para leituras e escritas, e falha automática. O desempenho é melhorado graças à colocação em cache dentro da memória para as operações de leitura e à transmissão em fluxo de alto débito para as operações de escrita. O cluster HBase pode ser criado no interior da rede virtual. Para obter mais detalhes, veja [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Criar clusters do HDInsight na Rede Virtual do Azure).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Como são geridos os dados no HBase do HDInsight?

É possível gerir os dados no HBase ao utilizar os comandos `create`, `get`, `put` e `scan` da shell do HBase. Os dados são escritos na base de dados através do comando `put` e lidos através do comando `get`. O comando `scan` é utilizado para obter dados de várias linhas numa tabela. É possível ainda gerir os dados através da API C# do HBase, que fornece uma biblioteca de cliente sobre a API REST do HBase. Uma base de dados HBase também pode ser consultada usando [a Colmeia Apache.](https://hive.apache.org/) Para uma introdução a estes modelos de programação, consulte [Começar a utilizar Apache HBase com Apache Hadoop em HDInsight](./apache-hbase-tutorial-get-started-linux.md). Os coprocessadores também estão disponíveis, que permitem o processamento de dados nos nós que acolhem a base de dados.

> [!NOTE]  
> O thrift não é suportado pelo HBase no HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Use caixas para Apache HBase

O caso de uso canónico para o qual o BigTable (e por extensão, HBase) foi criado a partir da pesquisa web. Os motores de busca criam índices que mapeiam termos para as páginas Web que os contêm. No entanto, o HBase é adequado para muitos outros casos de utilização, sendo que vários deles estão descritos nesta secção.

|Cenário |Descrição |
|---|---|
|Arquivo de chave-valor|O HBase pode ser usado como uma loja de valor chave, e é adequado para gerir sistemas de mensagens. O Facebook utiliza o HBase para o seu sistema de mensagens, e é ideal para armazenar e gerir comunicações na Internet. O WebTable utiliza o HBase para procurar e gerir tabelas extraídas de páginas Web.|
|Dados de sensores|O HBase é útil para capturar os dados que são recolhidos de forma incremental a partir de várias origens. Estes dados incluem análise social e séries de tempo. E manter os dashboards interativos atualizados com tendências e contadores, e gerir os sistemas de registo de auditoria. Exemplos incluem o terminal de trader da Bloomberg e a Base de Dados de Séries de Tempo Aberto (OpenTSDB). O OpenTSDB armazena e fornece acesso às métricas recolhidas sobre a saúde dos sistemas de servidores.|
|Consulta em tempo real|[Apache Phoenix](https://phoenix.apache.org/) é um motor de consulta SQL para Apache HBase. É acedido como um controlador JDBC, e permite consultar e gerir as tabelas HBase utilizando o SQL.|
|HBase como uma plataforma|As aplicações podem ser executadas sobre o HBase ao utilizá-lo como um arquivo de dados. Exemplos incluem Phoenix, OpenTSDB, `Kiji` e Titan. As aplicações também podem ser integradas no HBase. Exemplos incluem: [Apache Hive,](https://hive.apache.org/)Apache Pig, [Solr,](https://lucene.apache.org/solr/)Apache Storm, Apache Flume, [Apache Impala,](https://impala.apache.org/)Apache `Ganglia` Spark, e Apache Drill.|

## <a name="next-steps"></a>Próximos passos

* [Começar a usar Apache HBase com Apache Hadoop em HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Criar clusters do HDInsight na Rede Virtual do Azure)
* [Configure a replicação apache HBase em HDInsight](apache-hbase-replication.md)
