---
title: Tempos limite com o comando ' HBase hbck ' no Azure HDInsight
description: Problema de tempo limite com o comando ' HBase hbck ' ao corrigir atribuições de região
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 800182498ab77993d0861c9b5383e0d71b302b6f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091563"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Cenário: Tempos limite com o comando ' HBase hbck ' no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Encontre tempos limite com `hbase hbck` o comando ao corrigir atribuições de região.

## <a name="cause"></a>Causa

Uma possível causa de problemas de tempo limite quando você `hbck` usa o comando pode ser que várias regiões estejam no estado "em transição" por um longo tempo. Você pode ver essas regiões offline na interface do usuário do HBase Master. Como um grande número de regiões está tentando fazer a transição, HBase Master pode atingir o tempo limite e não conseguir colocar as regiões online novamente.

## <a name="resolution"></a>Resolução

1. Entre no cluster HBase do HDInsight usando SSH.

1. Execute `hbase zkcli` o comando para se conectar ao Apache ZooKeeper Shell.

1. Executar `rmr /hbase/regions-in-transition` ou`rmr /hbase-unsecure/regions-in-transition` comando.

1. Saia do `hbase zkcli` shell usando `exit` o comando.

1. Na interface do usuário do Apache Ambari, reinicie o serviço do Active HBase Master.

1. Execute o comando `hbase hbck -fixAssignments`.

1. Monitore a interface do usuário do HBase Master "região em transição" para garantir que nenhuma região fique presa.

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
