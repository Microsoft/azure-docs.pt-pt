---
title: CPU pegged no cluster Apache HBase - Azure HDInsight
description: CpU de resolução de problemas no servidor da região no cluster Apache HBase em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887313"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Cenário: CPU pegged no servidor da região no cluster Apache HBase em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O processo de servidor da região Apache HBase começa a ocupar perto de 200% de CPU, fazendo com que os alertas disparem sobre o processo E Cluster HBase Master para não funcionarem na capacidade máxima.

## <a name="cause"></a>Causa

Se estiver a executar o cluster HBase v3.4, pode ter sido atingido por um potencial bug causado pela atualização do jdk para a versão 1.7.0_151. O sintoma que vemos é que o processo de servidor da região `top` começa a ocupar perto de 200% de CPU (para verificar esta execução `ps -aux | grep` do comando; se houver um processo que ocupe perto de 200% cpU obter o seu pid e confirmar que é processo de servidor da região por execução).

## <a name="resolution"></a>Resolução

1. Instale jdk 1.8 em todos os nós do cluster como abaixo:

    * Execute a `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`ação do guião. Certifique-se de selecionar a opção de correr em todos os nós.

    * Em alternativa, pode iniciar sessão em cada `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`nó individual e executar o comando .

1. Vá à Ambari `https://<clusterdnsname>.azurehdinsight.net`UI.

1. Navegue para O >De Si->**Avançado->Avançado** `hbase-env configs` e altere a variável `JAVA_HOME` para `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Guarde a mudança de config.

1. [Opcional, mas recomendado] [Lave todas as mesas no cluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Da Ambari UI novamente, reinicie todos os serviços HBase que precisam de ser reiniciados.

1. Dependendo dos dados do cluster, pode levar alguns minutos até uma hora para o cluster chegar a um estado estável. A forma como confirma que o cluster atinge o estado estável é verificando o HMaster UI (todos os servidores da região devem estar ativos) a partir de Ambari (atualização) ou a partir da camada hbase de headnode e, em seguida, executar o comando de estado.

Para verificar se a sua atualização foi bem sucedida, verifique se os processos HBase relevantes foram iniciados usando a versão java apropriada - por exemplo, para verificar o servidor da região como:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
