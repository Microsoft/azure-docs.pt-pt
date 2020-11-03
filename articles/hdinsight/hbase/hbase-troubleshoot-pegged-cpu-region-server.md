---
title: CPU pegged no cluster Apache HBase - Azure HDInsight
description: Resolução de problemas pegged CPU no servidor da região no cluster Apache HBase em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: bed73c3ccc7f514ffc9ff8f97534ae4b249834ce
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286985"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]