---
title: Problemas com servidores de região no Azure HDInsight
description: Problemas com servidores de região no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 377a75d098ab4238fadc16b218bc69235f2e732a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091554"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemas com servidores de região no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Cenário: Regiões não atribuídas

### <a name="issue"></a>Problema

Ao executar `hbase hbck` o comando, você verá uma mensagem de erro semelhante a:

```
multiple regions being unassigned or holes in the chain of regions
```

Na interface do usuário do Apache HBase Master, você pode ver o número de regiões desbalanceadas em todos os servidores de região. Em seguida, você pode `hbase hbck` executar o comando para ver os buracos na cadeia de região.

### <a name="cause"></a>Causa

Os orifícios podem ser o resultado de regiões offline.

### <a name="resolution"></a>Resolução

Corrija as atribuições. Siga as etapas abaixo para colocar as regiões não atribuídas de volta ao estado normal:

1. Entre no cluster HBase do HDInsight usando SSH.

1. Execute `hbase zkcli` o comando para se conectar ao shell do ZooKeeper.

1. Executar `rmr /hbase/regions-in-transition` ou`rmr /hbase-unsecure/regions-in-transition` comando.

1. Saia do shell do Zookeeper `exit` usando o comando.

1. Abra a interface do usuário do Apache Ambari e reinicie o serviço do Active HBase Master.

1. Execute `hbase hbck` o comando novamente (sem nenhuma outra opção). Verifique a saída e verifique se todas as regiões estão sendo atribuídas.

---

## <a name="scenario-dead-region-servers"></a>Cenário: Servidores de região inoperante

### <a name="issue"></a>Problema

Falha ao iniciar os servidores de região.

### <a name="cause"></a>Causa

Vários diretórios WAL de divisão.

1. Obter lista de WALs atuais: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Inspecione o `wals.out` arquivo. Se houver muitos diretórios de divisão (começando com *-divisão), o servidor de região provavelmente falhará devido a esses diretórios.

### <a name="resolution"></a>Resolução

1. Interrompa o HBase do portal do Ambari.

1. Execute `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` para obter uma lista atualizada de WALs.

1. Mova os diretórios * de divisão para uma pasta temporária, `splitWAL`e exclua os diretórios de divisão *.

1. Execute `hbase zkcli` o comando para se conectar ao shell do Zookeeper.

1. Execute `rmr /hbase-unsecure/splitWAL`.

1. Reinicie o serviço HBase.

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
