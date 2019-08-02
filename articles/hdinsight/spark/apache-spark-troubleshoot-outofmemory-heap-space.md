---
title: Erro de espaço de heap de Java ao tentar abrir o servidor de histórico de Apache Spark no Azure HDInsight
description: O servidor Apache Livy falha ao iniciar com Java. lang. OutOfMemoryError no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667793"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>Cenário: Erro de espaço de heap de Java ao tentar abrir o servidor de histórico de Apache Spark no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe o seguinte erro ao abrir eventos no servidor de histórico do Spark:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>Causa

Esse problema geralmente é causado por uma falta de recursos ao abrir grandes arquivos Spark-Event. O tamanho do heap do Spark é definido como 1 GB por padrão, mas grandes arquivos de eventos do Spark podem exigir mais do que isso.

Se você quiser verificar o tamanho dos arquivos que está tentando carregar, poderá executar os seguintes comandos:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>Resolução

Você pode aumentar a memória do servidor de histórico do Spark `SPARK_DAEMON_MEMORY` editando a propriedade na configuração do Spark e reiniciando todos os serviços.

Você pode fazer isso de dentro da interface do usuário do navegador do Ambari selecionando a seção Spark2/config/Advanced Spark2-env.

![Seção spark2-env avançada](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

Adicione a seguinte propriedade para alterar a memória do servidor de histórico do Spark de 1g `SPARK_DAEMON_MEMORY=4g`para 4G:.

![Propriedade do Spark](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Certifique-se de reiniciar todos os serviços afetados do Ambari.

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
