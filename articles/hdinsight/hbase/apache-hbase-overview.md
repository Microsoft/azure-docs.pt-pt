---
title: O que é o HBase no Azure HDInsight?
description: Uma introdução ao Apache HBase no HDInsight, uma base de dados NoSQL baseada no Hadoop. Saiba mais sobre casos de utilização e compare o HBase a outros clusters do Hadoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: d109a1a233aaaf1f3922561106caa15781e31e01
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337351"
---
# <a name="what-is-apache-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-apache-hadoop"></a>O que é o Apache HBase no HDInsight: Uma base de dados NoSQL que fornece capacidades semelhantes a BigTable para o Apache Hadoop
[O Apache HBase](https://hbase.apache.org/) é uma base de dados de NoSQL de código-fonte aberto, qual é criado [Apache Hadoop](https://hadoop.apache.org/) e modelados após [Google BigTable](https://cloud.google.com/bigtable/). O HBase fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semiestruturados numa base de dados sem esquema, organizada por famílias de colunas.

Da perspectiva do usuário, o HBase é semelhante a uma base de dados. Os dados são armazenados em linhas e colunas de uma tabela e dados de uma linha são agrupados por família de colunas. O HBase é uma base de dados sem esquema uma vez que não é preciso definir as colunas nem os tipos de dados nelas armazenados antes de os utilizar. O código open source é dimensionado linearmente para processar petabytes de dados em milhares de nós. Pode depender da redundância de dados, do processamento em lotes e de outras funcionalidades fornecidas por aplicações distribuídas do ecossistema do Hadoop.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Como o Apache HBase é implementado no Azure HDInsight?

O HBase do HDInsight é fornecido como um cluster gerido que está integrado no ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no [armazenamento do Azure](./../hdinsight-hadoop-use-blob-storage.md) que proporciona baixa latência e uma maior elasticidade em termos de opções de desempenho e custo. Isto permite aos clientes criar sites interativos que funcionam com grandes conjuntos de dados, criar serviços que armazenam dados telemétricos e de sensores de milhões de pontos finais e analisar esses dados com tarefas do Hadoop. O HBase e o Hadoop são bons pontos de partida para projetos de macrodados no Azure; em particular, podem permitir que as aplicações em tempo real trabalhem com grandes conjuntos de dados.

A implementação do HDInsight tira partido da arquitetura de escalamento horizontal do HBase para fornecer fragmentação automática de tabelas, consistência forte para operações de leitura e escrita e ativação pós-falha automática. O desempenho é melhorado graças à colocação em cache dentro da memória para as operações de leitura e à transmissão em fluxo de alto débito para as operações de escrita. O cluster HBase pode ser criado no interior da rede virtual. Para obter mais detalhes, veja [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Criar clusters do HDInsight na Rede Virtual do Azure).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Como são geridos os dados no HBase do HDInsight?
É possível gerir os dados no HBase ao utilizar os comandos `create`, `get`, `put` e `scan` da shell do HBase. Os dados são escritos na base de dados através do comando `put` e lidos através do comando `get`. O comando `scan` é utilizado para obter dados de várias linhas numa tabela. É possível ainda gerir os dados através da API C# do HBase, que fornece uma biblioteca de cliente sobre a API REST do HBase. Uma base de dados do HBase também pode ser consultada ao utilizar [Apache Hive](https://hive.apache.org/). Para obter uma introdução a estes modelos de programação, consulte [começar a utilizar o Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md). Estão também disponíveis coprocessadores, que permitem o processamento de dados nos nós que alojam a base de dados.

> [!NOTE]  
> O thrift não é suportado pelo HBase no HDInsight.

## <a name="scenarios-use-cases-for-apache-hbase"></a>Cenários: Casos de utilização para o Apache HBase
O caso de utilização canónico para o qual o BigTable (e, por extensão, o HBase) foi criado a partir de pesquisa na web. Os motores de busca criam índices que mapeiam termos para as páginas Web que os contêm. No entanto, o HBase é adequado para muitos outros casos de utilização, sendo que vários deles estão descritos nesta secção.

* Arquivo de chave-valor
  
    O HBase pode ser utilizado como um arquivo de chave-valor e é adequado para gerir sistemas de mensagens. O Facebook utiliza o HBase no seu sistema de mensagens e este é ideal para armazenar e gerir comunicações na Internet. O WebTable utiliza o HBase para procurar e gerir tabelas extraídas de páginas Web.
* Dados de sensores
  
    O HBase é útil para capturar os dados que são recolhidos de forma incremental a partir de várias origens. Isto inclui a análise de dados sociais, as séries de tempo, a atualização de dashboards interativos com tendências e contadores e a gestão de sistemas de registo de auditoria. Os exemplos incluem o terminal comercial Bloomberg e o OpenTSDB (Open Time Series Database), que armazena e fornece acesso a métricas recolhidas sobre o estado de funcionamento dos sistemas de servidor.
* Consulta em tempo real
  
    [O Apache Phoenix](https://phoenix.apache.org/) é um motor de consulta SQL para o Apache HBase. Está acessível como um controlador JDBC e permite a consulta e gestão de tabelas do HBase através de SQL.
* HBase como uma plataforma
  
    As aplicações podem ser executadas sobre o HBase ao utilizá-lo como um arquivo de dados. Os exemplos incluem Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji e Titan. As aplicações também podem ser integradas no HBase. Os exemplos incluem [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [ Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/), e [Apache Drill](https://drill.apache.org/).

## <a name="next-steps"></a>Passos seguintes
* [Começar a utilizar o Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Criar clusters do HDInsight na Rede Virtual do Azure)
* [Configurar a replicação de Apache HBase no HDInsight](apache-hbase-replication.md)
* [Utilizar o Apache Maven para construir aplicações Java que utilizam o HBase do Apache com o HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md)

## <a name="see-also"></a>Ver também
* [Apache HBase](https://hbase.apache.org/)
* [Guia de referência do HBase do Apache](https://hbase.apache.org/book.html)
* [Bigtable: Um sistema de armazenamento distribuído para dados estruturados](https://research.google.com/archive/bigtable.html)
