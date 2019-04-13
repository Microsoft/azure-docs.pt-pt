---
title: Melhorar o desempenho de cargas de trabalho do Apache Spark com o Azure HDInsight e/s de Cache (pré-visualização)
description: Saiba mais sobre a Cache de e/s do Azure HDInsight e como usá-lo para melhorar o desempenho do Apache Spark.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: 5422f4796d2451b652222332799d8ad1a9fc4803
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525600"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache-preview"></a>Melhorar o desempenho de cargas de trabalho do Apache Spark com o Azure HDInsight e/s de Cache (pré-visualização)

Cache de e/s é um serviço de colocação em cache de dados para o Azure HDInsight, que melhora o desempenho das tarefas do Apache Spark. Cache de e/s também funciona com [Apache TEZ](https://tez.apache.org/) e [Apache Hive](https://hive.apache.org/) cargas de trabalho, que podem ser executadas no [Apache Spark](https://spark.apache.org/) clusters. E/s de Cache utiliza um componente de colocação em cache de código-fonte aberto chamado RubiX. RubiX é uma cache de disco local para utilização com motores de análise de macrodados que aceder a dados de sistemas de armazenamento na cloud. RubiX é exclusivo entre sistemas, a colocação em cache, porque utiliza Solid-State unidades (SSDs) em vez de reserva de memória operacionais para fins de colocação em cache. O serviço de Cache de e/s inicia e gere RubiX metadados servidores em cada nó de trabalho do cluster. Ele também configura todos os serviços do cluster para utilização transparente de RubiX cache.

A maioria dos SSDs fornecem mais do que 1 GByte por segundo de largura de banda. Essa largura de banda, complementada pela cache de ficheiros de dentro da memória do sistema operativo, fornece a largura de banda suficiente para carregar os motores de processamento de computação de grandes volumes de dados, como o Apache Spark. A memória operacional fica disponível para o Apache Spark processar tarefas de bastante dependente de memória, como shuffles. Ter uso exclusivo de operar memória permite o Apache Spark alcançar a utilização de recursos ideal.  

>[!Note]  
>Atualmente, o Cache de e/s utiliza RubiX como um componente de colocação em cache, mas isso pode mudar em versões futuras do serviço. Utilize as interfaces de Cache de e/s e não se todas as dependências diretamente sobre a implementação de RubiX.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Benefícios da Cache de e/s do Azure HDInsight

Utilizar a Cache de e/s fornece um aumento de desempenho para as tarefas de leitura de dados do armazenamento de Blobs do Azure.

Não é preciso fazer alterações às suas tarefas do Spark para aumentos de desempenho ao utilizar a Cache de e/s. Quando o Cache de e/s é desativado, esse código do Spark poderia ler os dados remotamente do armazenamento de Blobs do Azure: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. Quando o Cache de e/s é ativado, a mesma linha de código faz com que uma leitura em cache por meio do Cache de e/s. No seguintes leituras, os dados são lidos localmente de SSD. Nós de trabalho no cluster do HDInsight estão equipados com unidades SSD ligadas localmente e dedicadas. Cache de e/s de HDInsight utiliza estas SSDs locais para colocar em cache, que fornece o nível mais baixo da latência e maximiza a largura de banda.

## <a name="getting-started"></a>Introdução

A Cache de e/s do Azure HDInsight é desativada por predefinição em pré-visualização. Cache de e/s está disponível no Azure HDInsight 3.6 + os clusters do Spark, que são executadas 2.3 do Apache Spark.  Para ativar a Cache de e/s, faça o seguinte:

1. Selecione o cluster de HDInsight no [do portal do Azure](https://portal.azure.com).

1. Na **descrição geral** página (aberta por predefinição quando seleciona o cluster) selecione **Ambari Home** sob **dashboards de clusters**.

1. Selecione o **Cache de e/s** serviço à esquerda.

1. Selecione **ações** e **ativar**.

    ![Ativar o serviço de Cache de e/s no Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "permitindo que o serviço de Cache de e/s no Ambari")

1. Confirme o reinício de todos os serviços afetados no cluster.

>[!NOTE]  
> Mesmo que a barra de progresso mostra ativada, o Cache de e/s, na verdade, não está ativado enquanto não reiniciar os outros serviços afetados.

## <a name="troubleshooting"></a>Resolução de problemas
  
Poderá receber erros de espaço em disco com o Spark tarefas depois de ativar a Cache de e/s. Estes erros ocorrerem porque o Spark também utiliza o armazenamento de disco local para armazenar dados durante operações de descarte. Spark poderá ficar sem espaço de SSD, depois de e/s de Cache está ativada e o espaço de armazenamento do Spark é reduzido o limite de tempo. A quantidade de espaço utilizado por padrões de e/s de Cache para metade do total de espaço SSD. A utilização de espaço em disco para a Cache de e/s pode ser configurada no Ambari. Se obtiver erros no espaço em disco, reduzir a quantidade de espaço SSD, utilizado para a Cache de e/s e reinicie o serviço. Para alterar o espaço em Cache de e/s, siga os passos abaixo:

1. No Apache Ambari, selecione o **HDFS** serviço à esquerda.

1. Selecione o **configurações** e **avançadas** separadores.

    ![Editar HDFS Advanced Configuration](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "editar HDFS avançadas de configuração")

1. Desloque para baixo e expandir a **core-site personalizado** área.

1. Localizar a propriedade **hadoop.cache.data.fullness.percentage**.

1. Altere o valor na caixa.

    ![Editar a porcentagem de plenitude do Cache de e/s](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "editar a porcentagem de plenitude do Cache de e/s")

1. Selecione **guardar** no canto superior direito.

1. Selecione **reinicie** > **reiniciar as**.

    ![Reinício tudo afetados](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "reinício tudo afetados")

1. Selecione **confirmar reinício todos os**.

Se isto não resultar, desative o Cache de e/s.

## <a name="next-steps"></a>Próximos Passos

- Leia mais sobre a Cache de e/s, incluindo testes de desempenho nesta mensagem de blogue: [Tarefas do Apache Spark ganhar até 9 x seja mais rápido com a Cache de e/s do HDInsight](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
