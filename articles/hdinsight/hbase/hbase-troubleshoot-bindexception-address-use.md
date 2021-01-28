---
title: BindException - Endereço já em uso no Azure HDInsight
description: BindException - Endereço já em uso no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: c0a7bc7629b5c2a2e458ba94d62e341f578fdd25
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946389"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Cenário: BindException - Endereço já em uso no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A operação de reinício de um Servidor regional Apache HBase não está concluída. A partir do `region-server.log` diretório in `/var/log/hbase` nos nos nosmes do trabalhador onde o servidor da região começa a falhar, pode ver uma mensagem de erro semelhante à seguinte:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Reiniciar servidores da região de Apache HBase durante a atividade de carga de trabalho pesada. Abaixo está o que acontece nos bastidores quando um utilizador inicia a operação de reinício no servidor da região HBase a partir da Apache Ambari UI:

1. O agente Ambari envia um pedido de paragem para o servidor da região.

1. O agente Ambari espera 30 segundos para que o servidor da região desligue graciosamente.

1. Se a sua aplicação continuar a ligar-se ao servidor da região, o servidor não será desligado imediatamente. O tempo limite de 30 segundos expira antes de ocorrer o encerramento.

1. Após 30 segundos, o agente Ambari envia um comando de força para `kill -9` o servidor da região.

1. Devido a esta paragem abrupta, embora o processo do servidor da região seja morto, a porta associada ao processo pode não ser libertada, o que eventualmente leva a `AddressBindException` .

## <a name="resolution"></a>Resolução

Reduza a carga nos servidores da região HBase antes de iniciar um reinício. Além disso, é uma boa ideia primeiro lavar todas as mesas. Para obter uma referência sobre como lavar as tabelas, consulte [HDInsight HBase: Como melhorar o tempo de reinício do cluster Apache HBase através de tabelas de descargas](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Em alternativa, tente reiniciar manualmente os servidores da região nos nóns do trabalhador utilizando os seguintes comandos:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Próximos passos

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).