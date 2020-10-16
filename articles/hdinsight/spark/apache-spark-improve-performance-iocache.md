---
title: Desempenho do Apache Spark - Azure HDInsight IO Cache (Preview)
description: Saiba mais sobre a Cache IO IO do Azure HDInsight e como usá-lo para melhorar o desempenho do Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/23/2019
ms.openlocfilehash: 3e724e6336163a092c9b4385324b1aa037295bb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081762"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Melhorar o desempenho das cargas de trabalho da Apache Spark utilizando a cache IO IO do Azure HDInsight

IO Cache é um serviço de cache de dados para a Azure HDInsight que melhora o desempenho dos empregos da Apache Spark. IO Cache também trabalha com cargas de carga [Apache TEZ](https://tez.apache.org/) e [Apache Hive,](https://hive.apache.org/) que podem ser executados em aglomerados [Apache Spark.](https://spark.apache.org/) IO Cache usa um componente de cache de código aberto chamado RubiX. RubiX é uma cache de disco local para uso com motores de análise de dados grandes que acedem a dados de sistemas de armazenamento em nuvem. RubiX é único entre os sistemas de caching, porque utiliza Solid-State Drives (SSDs) em vez de reservar memória operacional para fins de caching. O serviço IO Cache lança e gere os Servidores de Metadados RubiX em cada nó de trabalhador do cluster. Também configura todos os serviços do cluster para uso transparente da cache RubiX.

A maioria dos SSDs fornecem mais de 1 GByte por segundo de largura de banda. Esta largura de banda, complementada pela cache de ficheiros de sistema operativo na memória, fornece largura de banda suficiente para carregar grandes motores de processamento de computação de dados, como o Apache Spark. A memória operacional é deixada disponível para o Apache Spark processar tarefas fortemente dependentes da memória, como baralhadas. Ter uma utilização exclusiva da memória operacional permite ao Apache Spark obter uma utilização ótima do recurso.  

> [!Note]  
> A IO Cache utiliza atualmente o RubiX como componente de cache, mas isso pode mudar em futuras versões do serviço. Por favor, use interfaces IO Cache e não tome nenhuma dependência diretamente na implementação de RubiX.
>IO Cache só é suportado com Azure BLOB Storage neste momento.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Benefícios da Cache IO IO da Azure HDInsight

A utilização de IO Cache proporciona um aumento de desempenho para empregos que lêem dados da Azure Blob Storage.

Não é preciso fazer alterações nos seus trabalhos de Spark para ver o desempenho aumentar ao utilizar o IO Cache. Quando a cache IO estiver desativada, este código Spark leria os dados remotamente a partir do Armazenamento Azure Blob: `spark.read.load('wasbs:///myfolder/data.parquet').count()` . Quando a cache IO é ativada, a mesma linha de código causa uma leitura em cache através da Cache IO. Nas seguintes leituras, os dados são lidos localmente a partir de SSD. Os nós dos trabalhadores no cluster HDInsight estão equipados com unidades SSD dedicadas e anexadas localmente. HDInsight IO Cache usa estes SSDs locais para cache, que fornece o nível mais baixo de latência e maximiza a largura de banda.

## <a name="getting-started"></a>Introdução

A azure HDInsight IO Cache é desativado por padrão na pré-visualização. IO Cache está disponível em clusters Azure HDInsight 3.6+ Spark, que executam Apache Spark 2.3.  Para ativar a cache IO no HDInsight 4.0, faça os seguintes passos:

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. Selecione o serviço **IO Cache** à esquerda.

1. Selecione **Ações** **(Ações de Serviço** em HDI 3.6) e **Ative**.

    ![Habilitação do serviço IO Cache em Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Habilitação do serviço IO Cache em Ambari")

1. Confirme o reinício de todos os serviços afetados no cluster.

> [!NOTE]  
> Mesmo que a barra de progresso mostre ativada, a Cache IO não está ativada até reiniciar os outros serviços afetados.

## <a name="troubleshooting"></a>Resolução de problemas
  
Pode obter erros de espaço em disco executando trabalhos spark depois de ativar io Cache. Estes erros ocorrem porque a Spark também utiliza o armazenamento de discos locais para armazenar dados durante as operações de baralhar. A faísca pode ficar sem espaço SSD uma vez que a Cache IO esteja ativada e o espaço para armazenamento de faíscas seja reduzido. A quantidade de espaço utilizada pela IO Cache é padrão para metade do espaço SSD total. O uso do espaço em disco para IO Cache é configurável em Ambari. Se tiver erros de espaço em disco, reduza a quantidade de espaço SSD utilizado para cache IO e reinicie o serviço. Para alterar o espaço definido para IO Cache, faça os seguintes passos:

1. Em Apache Ambari, selecione o serviço **HDFS** à esquerda.

1. Selecione os **separadores Configs** e **Advanced.**

    ![Editar configuração avançada de HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Editar configuração avançada de HDFS")

1. Desloque-se para baixo e expanda a área **do núcleo personalizado.**

1. Localize a propriedade **hadoop.cache.data.fullness.percentagem**.

1. Mude o valor na caixa.

    ![Editar percentagem de plenitude io cache](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Editar percentagem de plenitude io cache")

1. **Selecione Guarde** no canto superior direito.

1. **Selecione reiniciar**  >  **reiniciar todos os afetados**.

    ![Apache Ambari reinicia todos os afetados](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Reiniciar todos os afetados")

1. **Selecione Confirmar Reiniciar tudo**.

Se não funcionar, desative a cache.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre iO Cache, incluindo referências de desempenho neste post de blog: [Apache Spark jobs gain até 9x speed up with HDInsight IO Cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
