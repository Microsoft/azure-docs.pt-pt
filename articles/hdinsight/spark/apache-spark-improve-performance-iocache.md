---
title: Desempenho apache Spark - Azure HDInsight IO Cache (Pré-visualização)
description: Saiba mais sobre o Azure HDInsight IO Cache e como usá-lo para melhorar o desempenho da Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 43875b87d26f144b85454077fd3c044c820132bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494980"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Melhorar o desempenho das cargas de trabalho da Apache Spark utilizando o Azure HDInsight IO Cache

IO Cache é um serviço de recolha de dados para o Azure HDInsight que melhora o desempenho dos empregos da Apache Spark. IO Cache também trabalha com cargas de trabalho [Apache TEZ](https://tez.apache.org/) e [Apache Hive,](https://hive.apache.org/) que podem ser executados em clusters [Apache Spark.](https://spark.apache.org/) IO Cache usa um componente de cache de código aberto chamado RubiX. RubiX é uma cache de disco local para uso com grandes motores de análise de dados que acedem a dados de sistemas de armazenamento em nuvem. RubiX é único entre os sistemas de cache, porque usa Unidades de Estado Sólido (SSDs) em vez de reservar memória de funcionamento para fins de cache. O serviço IO Cache lança e gere os Servidores de Metadados RubiX em cada nó de trabalhador do cluster. Também configura todos os serviços do cluster para uso transparente da cache RubiX.

A maioria dos SSDs fornece mais de 1 GByte por segundo de largura de banda. Esta largura de banda, complementada pela cache de ficheiros em memória do sistema operativo, fornece largura de banda suficiente para carregar motores de processamento de grandes dados computacionais, como o Apache Spark. A memória de funcionamento é deixada disponível para a Apache Spark processar tarefas fortemente dependentes da memória, tais como baralhadas. Ter uso exclusivo da memória operacional permite à Apache Spark obter uma utilização ótima dos recursos.  

> [!Note]  
> A IO Cache utiliza atualmente rubiX como componente de cache, mas isso pode mudar em futuras versões do serviço. Por favor, use interfaces IO Cache e não tome nenhuma dependência diretamente na implementação rubiX.
>O IO Cache só é suportado com o Armazenamento BloB Azure neste momento.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Benefícios do Azure HDInsight IO Cache

A utilização do IO Cache proporciona um aumento de desempenho para trabalhos que lêem dados do Armazenamento De Blob Azure.

Não é preciso fazer alterações nos seus trabalhos de Spark para ver o desempenho aumentar ao usar O Cache IO. Quando a IO Cache é desativada, este código Spark `spark.read.load('wasbs:///myfolder/data.parquet').count()`lê dados remotamente a partir do Armazenamento De Blob Azure: . Quando o IO Cache é ativado, a mesma linha de código causa uma leitura em cache através do IO Cache. Nas leituras seguintes, os dados são lidos localmente a partir de SSD. Os nós dos trabalhadores no cluster HDInsight estão equipados com unidades SSD dedicadas e ligadas localmente. O HDInsight IO Cache utiliza estes SSDs locais para o cache, que proporciona o nível mais baixo de latência e maximiza a largura de banda.

## <a name="getting-started"></a>Introdução

O Cache Azure HDInsight IO é desativado por predefinição. IO Cache está disponível em clusters De faísca SOnel Azure HDInsight 3.6+, que executam Apache Spark 2.3.  Para ativar a Cache IO no HDInsight 4.0, faça os seguintes passos:

1. De um navegador web, navegue até, `https://CLUSTERNAME.azurehdinsight.net`onde `CLUSTERNAME` está o nome do seu cluster.

1. Selecione o serviço **IO Cache** à esquerda.

1. Selecione **Ações** ( Ações de**Serviço** em HDI 3.6) e **Ative**.

    ![Habilitar o serviço IO Cache em Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Habilitar o serviço IO Cache em Ambari")

1. Confirme o reinício de todos os serviços afetados no cluster.

> [!NOTE]  
> Mesmo que a barra de progresso mostre ativada, o IO Cache não está realmente ativado até reiniciar os outros serviços afetados.

## <a name="troubleshooting"></a>Resolução de problemas
  
Pode ter erros de espaço em disco a executar trabalhos spark depois de ativar o IO Cache. Estes erros ocorrem porque a Spark também utiliza o armazenamento de discos locais para armazenar dados durante as operações de baralhar. A faísca pode ficar sem espaço SSD uma vez que a IO Cache esteja ativada e o espaço para armazenamento de faíscas é reduzido. A quantidade de espaço utilizado pela IO Cache falha em metade do espaço Total SSD. O uso do espaço do disco para IO Cache é configurável em Ambari. Se tiver erros de espaço em disco, reduza a quantidade de espaço SSD utilizado para o IO Cache e reinicie o serviço. Para alterar o espaço definido para IO Cache, faça os seguintes passos:

1. Em Apache Ambari, selecione o serviço **HDFS** à esquerda.

1. Selecione os **separadores Configs** e **Advanced.**

    ![Editar Configuração Avançada HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Editar Configuração Avançada HDFS")

1. Desloque-se para baixo e expanda a área **de núcleo personalizado.**

1. Localize a propriedade **hadoop.cache.data.fullness.percent**.

1. Mude o valor da caixa.

    ![Editar IO Cache Fullness Percentagem](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Editar IO Cache Fullness Percentagem")

1. Selecione **Guardar** na parte superior direita.

1. Selecione **Reiniciar** > **todos os afetados**.

    ![Apache Ambari reinicia todos os afetados](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Reiniciar todos os afetados")

1. Selecione **Confirmar Reiniciar tudo**.

Se isso não funcionar, desabilite o IO Cache.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre IO Cache, incluindo referências de desempenho neste post de blog: [Os empregos da Apache Spark ganham até 9x velocidade com HDInsight IO Cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
