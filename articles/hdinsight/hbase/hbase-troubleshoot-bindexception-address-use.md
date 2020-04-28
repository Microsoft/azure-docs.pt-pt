---
title: BindException - Endereço já em uso no Azure HDInsight
description: BindException - Endereço já em uso no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887347"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Cenário: BindException - Endereço já em uso no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A operação de reinício de um Servidor da Região Apache HBase não está concluída. A `region-server.log` partir `/var/log/hbase` do diretório nos nós dos trabalhadores onde o servidor da região falha, pode ver uma mensagem de erro semelhante à seguinte:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Reiniciar os Servidores da Região Apache HBase durante a atividade de carga de trabalho pesada. Abaixo está o que acontece nos bastidores quando um utilizador inicia a operação de reinício no servidor da região HBase da Apache Ambari UI:

1. O agente Ambari envia um pedido de stop para o servidor da região.

1. O agente Ambari espera 30 segundos para que o servidor da região desligue graciosamente.

1. Se a sua aplicação continuar a ligar-se ao servidor da região, o servidor não desliga imediatamente. O intervalo de 30 segundos expira antes do encerramento.

1. Após 30 segundos, o agente Ambari`kill -9`envia um comando de morte forçada para o servidor da região.

1. Devido a esta paragem abrupta, embora o processo de servidor da região seja morto, a `AddressBindException`porta associada ao processo pode não ser libertada, o que eventualmente leva a .

## <a name="resolution"></a>Resolução

Reduza a carga nos servidores da região HBase antes de começar um reinício. Além disso, é uma boa ideia primeiro dar descarga em todas as mesas. Para obter uma referência sobre como descarregar tabelas, consulte [HDInsight HBase: Como melhorar o tempo de reinício do cluster Apache HBase através de tabelas de descarga](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Em alternativa, tente reiniciar manualmente os servidores da região nos nós dos trabalhadores utilizando os seguintes comandos:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
