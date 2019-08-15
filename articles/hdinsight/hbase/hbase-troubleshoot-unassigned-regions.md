---
title: Problemas com servidores de região no Azure HDInsight
description: Problemas com servidores de região no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: e75f2fdd0530b92e8c8405b74c2a364ff9e9e28e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935435"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemas com servidores de região no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Cenário: Regiões não atribuídas

### <a name="issue"></a>Problema

Ao executar `hbase hbck` o comando, você verá uma mensagem de erro semelhante a:

```
multiple regions being unassigned or holes in the chain of regions
```

Na interface do usuário do Apache HBase Master, pode ser visto que a contagem de regiões que estão sendo desbalanceadas em todos os servidores de região.

### <a name="cause"></a>Causa

Os orifícios podem ser o resultado de regiões offline.

### <a name="resolution"></a>Resolução

Corrija as atribuições. Siga as etapas abaixo para colocar as regiões não atribuídas de volta ao estado normal:

1. Entre no cluster HBase do HDInsight usando SSH.

1. Execute `hbase zkcli` o comando para se conectar ao shell do Zookeeper.

1. Executar `rmr /hbase/regions-in-transition` ou`rmr /hbase-unsecure/regions-in-transition` comando.

1. Saia do shell do Zookeeper `exit` usando o comando.

1. Abra a interface do usuário do Ambari e reinicie o Active HBase Master Service do Ambari.

1. Execute `hbase hbck` o comando novamente (sem nenhuma outra opção). Verifique a saída e verifique se todas as regiões estão sendo atribuídas.

---

## <a name="scenario-dead-region-servers"></a>Cenário: Servidores de região inoperante

### <a name="issue"></a>Problema

Falha ao iniciar os servidores de região.

### <a name="cause"></a>Causa

Vários diretórios WAL de divisão.

1. Obter lista de Wals atuais: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Inspecione o `wals.out` arquivo. Se houver muitos diretórios de divisão (começando com *-divisão), o servidor de região provavelmente falhará devido a esses diretórios.

### <a name="resolution"></a>Resolução

1. Interrompa o HBase do portal do Ambari.

1. Execute `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` para obter uma lista atualizada de WALs.

1. Mova os diretórios * de divisão para uma pasta temporária, `splitWAL`e exclua os diretórios de divisão *.

1. Execute `hbase zkcli` o comando para se conectar ao shell do Zookeeper.

1. Execute `rmr /hbase-unsecure/splitWAL`.

1. Reinicie o serviço HBase.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
