---
title: Bindexception-o endereço já está em uso no Azure HDInsight
description: Bindexception-o endereço já está em uso no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887347"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Cenário: Bindexception-o endereço já está em uso no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A operação de reinicialização em um servidor de região do Apache HBase não é concluída. Na `region-server.log` no diretório `/var/log/hbase` nos nós de trabalho em que o servidor de região é iniciado, você poderá ver uma mensagem de erro semelhante à seguinte:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Reiniciando servidores de região do Apache HBase durante atividade de carga de trabalho pesada. Abaixo está o que acontece nos bastidores quando um usuário inicia a operação de reinicialização no servidor de região do HBase da interface do usuário do Apache amAmbari:

1. O agente Ambari envia uma solicitação de interrupção para o servidor de região.

1. O agente Ambari aguarda 30 segundos para que o servidor de região seja desligado normalmente

1. Se o seu aplicativo continuar a se conectar com o servidor de região, o servidor não será desligado imediatamente. O tempo limite de 30 segundos expira antes de o desligamento ocorrer.

1. Após 30 segundos, o agente Ambari envia um comando Force-Kill (`kill -9`) para o servidor de região.

1. Devido a esse desligamento abrupta, embora o processo do servidor de região seja encerrado, a porta associada ao processo pode não ser liberada, o que, eventualmente, leva a `AddressBindException`.

## <a name="resolution"></a>Resolução

Reduza a carga nos servidores de região do HBase antes de iniciar uma reinicialização. Além disso, é uma boa ideia primeiro liberar todas as tabelas. Para obter uma referência sobre como liberar tabelas, confira [HDInsight HBase: como melhorar o tempo de reinicialização do cluster do Apache HBase liberando tabelas](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Como alternativa, tente reiniciar manualmente os servidores de região nos nós de trabalho usando os seguintes comandos:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
