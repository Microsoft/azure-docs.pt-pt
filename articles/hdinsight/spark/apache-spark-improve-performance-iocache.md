---
title: Desempenho de Apache Spark-cache de e/s do Azure HDInsight (versão prévia)
description: Saiba mais sobre o cache de e/s do Azure HDInsight e como usá-lo para melhorar o desempenho de Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 43875b87d26f144b85454077fd3c044c820132bf
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75494980"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Melhorar o desempenho de cargas de trabalho de Apache Spark usando o cache de e/s do Azure HDInsight

O cache de e/s é um serviço de cache de dados para o Azure HDInsight que melhora o desempenho de trabalhos de Apache Spark. O cache de e/s também funciona com as cargas de trabalho do [Apache tez](https://tez.apache.org/) e [Apache Hive](https://hive.apache.org/) , que podem ser executadas em clusters [Apache Spark](https://spark.apache.org/) . O cache de e/s usa um componente de cache de código aberto chamado RubiX. RubiX é um cache de disco local para uso com Big Data mecanismos de análise que acessam dados de sistemas de armazenamento em nuvem. O RubiX é exclusivo entre sistemas de cache, pois usa SSDs (unidades de estado sólido) em vez de reservar a memória operacional para fins de cache. O serviço de cache de e/s inicia e gerencia servidores de metadados RubiX em cada nó de trabalho do cluster. Ele também configura todos os serviços do cluster para uso transparente do cache RubiX.

A maioria dos SSDs fornece mais de 1 GB por segundo de largura de banda. Essa largura de banda, complementada pelo cache de arquivo na memória do sistema operacional, fornece largura de banda suficiente para carregar Big Data mecanismos de processamento de computação, como Apache Spark. A memória operacional é deixada disponível para Apache Spark processar tarefas dependentes de memória intensas, como embaralhamentos. Ter uso exclusivo da memória operacional permite que Apache Spark obter o uso de recursos ideal.  

> [!Note]  
> O cache de e/s atualmente usa RubiX como um componente de cache, mas isso pode ser alterado em versões futuras do serviço. Use interfaces de cache de e/s e não faça dependências diretamente na implementação de RubiX.
>O cache de e/s só tem suporte no armazenamento de BLOBs do Azure no momento.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Benefícios do cache de e/s do Azure HDInsight

Usar o cache de e/s fornece um aumento de desempenho para trabalhos que lêem dados do armazenamento de BLOBs do Azure.

Você não precisa fazer nenhuma alteração em seus trabalhos do Spark para ver o aumento de desempenho ao usar o cache de e/s. Quando o cache de e/s está desabilitado, este código Spark lê dados remotamente do armazenamento de BLOBs do Azure: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. Quando o cache de e/s é ativado, a mesma linha de código causa uma leitura em cache do cache de e/s. Nas leituras a seguir, os dados são lidos localmente do SSD. Os nós de trabalho no cluster HDInsight são equipados com unidades SSD dedicadas localmente anexadas. O cache de e/s do HDInsight usa esses SSDs locais para caching, que fornece o nível mais baixo de latência e maximiza a largura de banda.

## <a name="getting-started"></a>Introdução

O cache de e/s do Azure HDInsight é desativado por padrão na visualização. O cache de e/s está disponível em clusters do Azure HDInsight 3.6 + Spark, que executam o Apache Spark 2,3.  Para ativar o cache de e/s no HDInsight 4,0, execute as seguintes etapas:

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Selecione o serviço de **cache de e/s** à esquerda.

1. Selecione **ações** (**ações de serviço** no HDI 3,6) e **ative**.

    ![Habilitando o serviço de cache de e/s no Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Habilitando o serviço de cache de e/s no Ambari")

1. Confirme a reinicialização de todos os serviços afetados no cluster.

> [!NOTE]  
> Embora a barra de progresso mostre a ativação, o cache de e/s não é habilitado de fato até que você reinicie os outros serviços afetados.

## <a name="troubleshooting"></a>Resolução de problemas
  
Você pode obter erros de espaço em disco executando trabalhos do Spark depois de habilitar o cache de e/s. Esses erros ocorrem porque o Spark também usa o armazenamento em disco local para armazenar dados durante operações de embaralhamento. O Spark pode ficar sem espaço SSD quando o cache de e/s estiver habilitado e o espaço para o armazenamento do Spark for reduzido. A quantidade de espaço usada pelo cache de e/s usa como padrão a metade do espaço SSD total. O uso de espaço em disco para o cache de e/s é configurável em Ambari. Se você receber erros de espaço em disco, reduza a quantidade de espaço SSD usada para o cache de e/s e reinicie o serviço. Para alterar o espaço definido para o cache de e/s, execute as seguintes etapas:

1. No Apache Ambari, selecione o serviço **HDFS** à esquerda.

1. Selecione as guias **configurações** e **avançado** .

    ![Editar configuração avançada do HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Editar configuração avançada do HDFS")

1. Role para baixo e expanda a área **Core-site personalizada** .

1. Localize a propriedade **Hadoop. cache. Data. fullity. Percentage**.

1. Altere o valor na caixa.

    ![Editar porcentagem de total do cache de e/s](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Editar porcentagem de total do cache de e/s")

1. Selecione **salvar** no canto superior direito.

1. Selecione **reiniciar** > **reiniciar todos os afetados**.

    ![O Apache Ambari reinicia todos os afetados](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Reiniciar todos os afetados")

1. Selecione **confirmar reiniciar tudo**.

Se isso não funcionar, desabilite o cache de e/s.

## <a name="next-steps"></a>Próximos Passos

Leia mais sobre o cache de e/s, incluindo benchmarks de desempenho nesta postagem de blog: [Apache Spark trabalhos obterem até 9x acelerar com o cache de e/s do HDInsight](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
