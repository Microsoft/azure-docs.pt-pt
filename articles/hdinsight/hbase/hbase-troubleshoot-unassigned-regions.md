---
title: Problemas com servidores da região em Azure HDInsight
description: Problemas com servidores da região em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 968a0c6e1717245171bf84821a58cad4e440046e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936608"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemas com servidores da região em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Cenário: Regiões não assinadas

### <a name="issue"></a>Problema

Ao executar `hbase hbck` o comando, vê uma mensagem de erro semelhante a:

```
multiple regions being unassigned or holes in the chain of regions
```

A partir do Apache HBase Master UI, você pode ver o número de regiões que estão desequilibradas em todos os servidores da região. Depois, pode executar `hbase hbck` o comando para ver buracos na cadeia da região.

### <a name="cause"></a>Causa

Os furos podem ser o resultado de regiões offline.

### <a name="resolution"></a>Resolução

Conserte as tarefas. Siga os passos abaixo para trazer as regiões não assinadas de volta ao estado normal:

1. Inscreva-se no cluster HDInsight HBase utilizando SSH.

1. Executar `hbase zkcli` o comando para ligar com a concha do ZooKeeper.

1. Correr `rmr /hbase/regions-in-transition` ou `rmr /hbase-unsecure/regions-in-transition` comandar.

1. Saia da concha do zookeeper usando `exit` o comando.

1. Abra o Apache Ambari UI e, em seguida, reinicie o serviço Ative HBase Master.

1. Executar `hbase hbck` o comando novamente (sem mais opções). Verifique a saída e certifique-se de que todas as regiões estão a ser atribuídas.

---

## <a name="scenario-dead-region-servers"></a>Cenário: Servidores da região morta

### <a name="issue"></a>Problema

Os servidores da região não começam.

### <a name="cause"></a>Causa

Vários diretórios wal divididos.

1. Obtenha a lista dos WALs atuais: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` .

1. Inspecione o `wals.out` ficheiro. Se houver demasiados diretórios divididos (começando com a divisão de si), o servidor da região provavelmente está a falhar por causa destes diretórios.

### <a name="resolution"></a>Resolução

1. Pare a Base H do portal Ambari.

1. Execute `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` para obter uma nova lista de WALs.

1. Mover os diretórios de divisão para uma pasta `splitWAL` temporária, e apagar os diretórios de divisão.

1. Execute `hbase zkcli` o comando para se conectar com a concha do zookeeper.

1. Executar `rmr /hbase-unsecure/splitWAL` .

1. Reiniciar o serviço HBase.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).