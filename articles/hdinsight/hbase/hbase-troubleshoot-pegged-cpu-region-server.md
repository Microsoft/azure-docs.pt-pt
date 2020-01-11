---
title: CPU vinculada no cluster apache HBase – Azure HDInsight
description: Solucionar problemas de CPU vinculada no servidor de região no cluster do Apache HBase no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887313"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Cenário: CPU vinculada no servidor de região no cluster apache HBase no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O processo do servidor de região do Apache HBase começa aproximadamente 200% da CPU, fazendo com que os alertas sejam acionados em HBase Master processo e o cluster não funcionem com capacidade total.

## <a name="cause"></a>Causa

Se você estiver executando o cluster HBase v 3.4, talvez tenha sido atingido por um possível bug causado pela atualização do JDK para a versão 1.7.0 _151. O sintoma que vemos é que o processo do servidor de região começa aproximadamente 200% da CPU (para verificar isso, execute o comando `top`; se houver um processo ocupando aproximadamente 200% da CPU, obtenha seu PID e confirme se ele é um processo do servidor de região executando `ps -aux | grep`).

## <a name="resolution"></a>Resolução

1. Instale o JDK 1,8 em todos os nós do cluster, como mostrado abaixo:

    * Execute a ação de script `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`. Certifique-se de selecionar a opção para executar em todos os nós.

    * Como alternativa, você pode entrar em cada nó individual e executar o comando `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`.

1. Acesse Ambari UI-`https://<clusterdnsname>.azurehdinsight.net`.

1. Navegue até o **HBase-> Configurações-> Avançado-> `hbase-env configs` avançado** e altere a `JAVA_HOME` da variável para `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Salve a alteração de configuração.

1. [Opcional, mas recomendado] [Libera Todas as tabelas no cluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Na interface do usuário do Ambari novamente, reinicie todos os serviços HBase que precisam ser reiniciados.

1. Dependendo dos dados no cluster, pode levar alguns minutos até uma hora para o cluster alcançar o estado estável. A maneira como você confirma que o cluster atinge o estado estável é verificando a interface do usuário do HMaster (todos os servidores de região devem estar ativos) do Ambari (atualizar) ou do cabeçalho executar o Shell do HBase e executar o comando status.

Para verificar se a atualização foi bem-sucedida, verifique se os processos relevantes do HBase foram iniciados usando a versão do Java apropriada-por exemplo, para servidor de região, verifique como:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
