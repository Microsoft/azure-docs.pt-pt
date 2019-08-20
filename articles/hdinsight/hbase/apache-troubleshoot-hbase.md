---
title: Solucionar problemas do HBase usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o HBase e o Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573957"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Solucionar problemas do Apache HBase usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas resoluções ao trabalhar com cargas do Apache HBase no Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Como fazer executar relatórios de comando hbck com várias regiões não atribuídas?

Uma mensagem de erro comum que você pode ver ao executar o `hbase hbck` comando é "várias regiões que estão sendo não atribuídas ou buracos na cadeia de regiões".

Na interface do usuário do HBase Master, você pode ver o número de regiões desbalanceadas em todos os servidores de região. Em seguida, você pode `hbase hbck` executar o comando para ver os buracos na cadeia de região.

Os buracos podem ser causados por regiões offline, portanto, corrija as atribuições primeiro. 

Para colocar as regiões não atribuídas novamente em um estado normal, conclua as seguintes etapas:

1. Entre no cluster HBase do HDInsight usando SSH.
2. Para se conectar com o Shell do Apache ZooKeeper, `hbase zkcli` execute o comando.
3. Execute o `rmr /hbase/regions-in-transition` comando ou o `rmr /hbase-unsecure/regions-in-transition` comando.
4. Para sair do `hbase zkcli` Shell, use o `exit` comando.
5. Abra a interface do usuário do Apache Ambari e reinicie o serviço do Active HBase Master.
6. Execute o `hbase hbck` comando novamente (sem nenhuma opção). Verifique a saída desse comando para garantir que todas as regiões estejam sendo atribuídas.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Como fazer corrigir problemas de tempo limite ao usar comandos hbck para atribuições de região?

### <a name="issue"></a>Problema

Uma possível causa de problemas de tempo limite quando você `hbck` usa o comando pode ser que várias regiões estejam no estado "em transição" por um longo tempo. Você pode ver essas regiões offline na interface do usuário do HBase Master. Como um grande número de regiões está tentando fazer a transição, HBase Master pode atingir o tempo limite e não pode colocar essas regiões novamente online.

### <a name="resolution-steps"></a>Passos de resolução

1. Entre no cluster HBase do HDInsight usando SSH.
2. Para se conectar com o Shell do Apache ZooKeeper, `hbase zkcli` execute o comando.
3. Execute o `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` comando ou.
4. Para sair do `hbase zkcli` Shell, use o `exit` comando.
5. Na interface do usuário do amAmbari, reinicie o serviço do Active HBase Master.
6. Execute o `hbase hbck -fixAssignments` comando novamente.

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
