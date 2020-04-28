---
title: Problemas com servidores da região no Azure HDInsight
description: Problemas com servidores da região no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79272763"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemas com servidores da região no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Cenário: Regiões não atribuídas

### <a name="issue"></a>Problema

Ao `hbase hbck` executar o comando, vê uma mensagem de erro semelhante a:

```
multiple regions being unassigned or holes in the chain of regions
```

A partir do Apache HBase Master UI, você pode ver o número de regiões que estão desequilibradas em todos os servidores da região. Depois, pode `hbase hbck` sabotar o comando para ver buracos na cadeia da região.

### <a name="cause"></a>Causa

Buracos podem ser o resultado de regiões offline.

### <a name="resolution"></a>Resolução

Conserte as tarefas. Siga os passos abaixo para que as regiões não atribuídas voltem ao estado normal:

1. Inscreva-se no cluster HDInsight HBase utilizando o SSH.

1. Executar `hbase zkcli` comando para ligar com a concha zookeeper.

1. Correr `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` ou comandar.

1. Saia da concha `exit` do zookeeper usando o comando.

1. Abra o Apache Ambari UI e, em seguida, reinicie o serviço Ative HBase Master.

1. Executar `hbase hbck` o comando novamente (sem mais opções). Verifique a saída e certifique-se de que todas as regiões estão a ser atribuídas.

---

## <a name="scenario-dead-region-servers"></a>Cenário: Servidores da região morta

### <a name="issue"></a>Problema

Os servidores da região não conseguem arrancar.

### <a name="cause"></a>Causa

Vários diretórios wal spliting.

1. Obtenha a lista dos `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`WALs atuais: .

1. Inspecione `wals.out` o ficheiro. Se há demasiados diretórios de divisão (começando com a divisão), o servidor da região provavelmente está falhando por causa destes diretórios.

### <a name="resolution"></a>Resolução

1. Pare a Base H Base do portal Ambari.

1. Execute `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` para obter uma nova lista de WALs.

1. Mova os diretórios de divisão para `splitWAL`uma pasta temporária e elimine os diretórios de divisão.

1. Execute `hbase zkcli` o comando para se conectar com a concha do zookeeper.

1. Executar `rmr /hbase-unsecure/splitWAL`.

1. Reiniciar o serviço HBase.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
