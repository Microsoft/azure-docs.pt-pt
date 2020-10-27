---
title: CPU pegged no cluster Apache HBase - Azure HDInsight
description: Resolução de problemas pegged CPU no servidor da região no cluster Apache HBase em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: ab9aa6d7be83faa0c26951b2c45092bbcb063d79
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540215"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Cenário: CPU pegged no servidor da região no cluster Apache HBase em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O processo do servidor da região Apache HBase começa a ocupar perto de 200% de CPU, fazendo com que os alertas disparem sobre o processo HBase Master e o cluster não funcionem na capacidade máxima.

## <a name="cause"></a>Causa

Se estiver a executar o cluster HBase v3.4, pode ter sido atingido por um bug potencial causado pela atualização do JDK para a versão 1.7.0_151. O sintoma que vemos é que o processo do servidor da região começa a ocupar perto de 200% de CPU (para verificar esta execução do `top` comando; se houver um processo que ocupa perto de 200% de CPU obtenha o seu pid e confirme que é processo de servidor regional `ps -aux | grep` executando).

## <a name="resolution"></a>Resolução

1. Instale jdk 1.8 em todos os nós do cluster como abaixo:

    * Executar a ação do script `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Certifique-se de selecionar a opção para correr em todos os nós.

    * Em alternativa, pode inscrever-se em cada nó individual e executar o comando `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Vá para a UI Ambari `https://<clusterdnsname>.azurehdinsight.net` - .

1. Navegue para **HBase->Configs->Advanced->Advanced e** `hbase-env configs` altere a variável `JAVA_HOME` para `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Poupe a mudança de config.

1. [Opcional, mas recomendado] [Lave todas as mesas no cluster.](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables)

1. A partir da UI Ambari novamente, reinicie todos os serviços HBase que precisam de ser reiniciados.

1. Dependendo dos dados do cluster, pode levar alguns minutos até uma hora para o cluster alcançar um estado estável. A forma como confirma que o cluster atinge o estado estável é verificando o HMaster UI (todos os servidores da região devem estar ativos) a partir de Ambari (refresh) ou a partir do headnode run HBase shell e, em seguida, executar o comando de estado.

Para verificar se a sua atualização foi bem sucedida, verifique se os processos HBase relevantes são iniciados usando a versão java apropriada - por exemplo, para verificação do servidor da região como:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).