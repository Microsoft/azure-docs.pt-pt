---
title: O que é Apache HBase em Azure HDInsight?
description: Uma introdução ao Apache HBase no HDInsight, uma base de dados NoSQL baseada no Hadoop. Saiba mais sobre casos de utilização e compare o HBase a outros clusters do Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: e977d4d68a330f57c4b53da4270e4515d4e69ee0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729812"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>O que é Apache HBase em Azure HDInsight

[Apache HBase](https://hbase.apache.org/) é uma base de dados noSQL de código aberto que é construída em Apache Hadoop e modelada após [o Google BigTable](https://cloud.google.com/bigtable/). A HBase fornece acesso aleatório e forte consistência para grandes quantidades de dados numa base de dados sem esquemas. A base de dados é organizada por famílias de colunas.

Do ponto de vista do utilizador, o HBase é semelhante a uma base de dados. Os dados são armazenados nas linhas e colunas de uma tabela, e os dados dentro de uma linha são agrupados pela família das colunas. HBase é uma base de dados sem esquemas. As colunas e os tipos de dados podem ser indefinidos antes de as utilizar. O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. Pode contar com a redundância de dados, o processamento de lotes e outras funcionalidades que são fornecidas por aplicações distribuídas no ambiente Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Como é implementada a Apache HBase no Azure HDInsight?

O HBase do HDInsight é fornecido como um cluster gerido que está integrado no ambiente do Azure. Os clusters estão configurados para armazenar dados diretamente no [Armazenamento Azure,](./../hdinsight-hadoop-use-blob-storage.md)que proporciona baixa latência e maior elasticidade no desempenho e nas escolhas de custos. Esta propriedade permite que os clientes construam websites interativos que trabalham com grandes conjuntos de dados. Para construir serviços que armazenam dados de sensores e telemetria de milhões de pontos finais. E analisar estes dados com empregos hadoop. HBase e Hadoop são bons pontos de partida para o projeto big data em Azure. Os serviços podem permitir que aplicações em tempo real funcionem com grandes conjuntos de dados.

A implementação do HDInsight utiliza a arquitetura de escala-out da HBase para fornecer sharding automático de tabelas. E forte consistência para leituras e escritos, e falha automática. O desempenho é melhorado graças à colocação em cache dentro da memória para as operações de leitura e à transmissão em fluxo de alto débito para as operações de escrita. O cluster HBase pode ser criado no interior da rede virtual. Para obter mais detalhes, veja [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Criar clusters do HDInsight na Rede Virtual do Azure).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Como são geridos os dados no HBase do HDInsight?

É possível gerir os dados no HBase ao utilizar os comandos `create`, `get`, `put` e `scan` da shell do HBase. Os dados são escritos na base de dados através do comando `put` e lidos através do comando `get`. O comando `scan` é utilizado para obter dados de várias linhas numa tabela. É possível ainda gerir os dados através da API C# do HBase, que fornece uma biblioteca de cliente sobre a API REST do HBase. Uma base de dados HBase também pode ser consultada utilizando [a Hive Apache](https://hive.apache.org/). Para uma introdução a estes modelos de programação, consulte [Get started using Apache HBase with Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md). Também estão disponíveis coprocessadores, que permitem o processamento de dados nos nós que acolhem a base de dados.

> [!NOTE]  
> O thrift não é suportado pelo HBase no HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Utilize casos para Apache HBase

O caso canónico de utilização para o qual o BigTable (e por extensão, HBase) foi criado a partir da pesquisa web. Os motores de busca criam índices que mapeiam termos para as páginas Web que os contêm. No entanto, o HBase é adequado para muitos outros casos de utilização, sendo que vários deles estão descritos nesta secção.

|Cenário |Descrição |
|---|---|
|Arquivo de chave-valor|O HBase pode ser usado como uma loja de valor-chave, e é adequado para gerir sistemas de mensagens. O Facebook utiliza hbase para o seu sistema de mensagens, e é ideal para armazenar e gerir comunicações de Internet. O WebTable utiliza o HBase para procurar e gerir tabelas extraídas de páginas Web.|
|Dados de sensores|O HBase é útil para capturar os dados que são recolhidos de forma incremental a partir de várias origens. Estes dados incluem análise social e séries temporais. E manter os dashboards interativos atualizados com tendências e contadores, e gerir sistemas de registo de auditoria. Exemplos incluem o terminal de trader bloomberg e a Base de Dados de Séries de Tempo Aberto (OpenTSDB). As lojas OpenTSDB e fornecem acesso a métricas recolhidas sobre a saúde dos sistemas de servidores.|
|Consulta em tempo real|[Apache Phoenix](https://phoenix.apache.org/) é um motor de consulta SQL para Apache HBase. É acedido como um controlador JDBC, e permite consultar e gerir tabelas HBase usando SQL.|
|HBase como uma plataforma|As aplicações podem ser executadas sobre o HBase ao utilizá-lo como um arquivo de dados. Exemplos incluem Phoenix, OpenTSDB `Kiji`e Titan. As aplicações também podem ser integradas no HBase. Exemplos incluem: [Apache Hive,](https://hive.apache.org/)Apache Pig, [Solr,](https://lucene.apache.org/solr/)Apache Storm, Apache `Ganglia`Flume, Apache [Impala,](https://impala.apache.org/)Apache Spark e Apache Drill.|

## <a name="next-steps"></a>Passos seguintes

* [Começar a usar Apache HBase com Apache Hadoop em HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Criar clusters do HDInsight na Rede Virtual do Azure)
* [Configure a replicação de Apache HBase no HDInsight](apache-hbase-replication.md)
