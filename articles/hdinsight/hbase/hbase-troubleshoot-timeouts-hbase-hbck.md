---
title: Tempos limite com o comando “hbase hbck” no Azure HDInsight
description: Problema de time out com comando 'hbase hbck' ao fixar atribuições da região
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887194"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Cenário: Timeouts com comando 'hbase hbck' em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Encontre intervalos com `hbase hbck` comando ao fixar atribuições da região.

## <a name="cause"></a>Causa

Uma potencial causa para problemas de tempo limite quando se usa o `hbck` comando pode ser que várias regiões estejam no estado de "transição" por muito tempo. Pode ver essas regiões como offline no HBase Master UI. Como um grande número de regiões estão a tentar fazer a transição, o HBase Master pode sair e não conseguir repor essas regiões.

## <a name="resolution"></a>Resolução

1. Inscreva-se no cluster HDInsight HBase utilizando SSH.

1. Executar `hbase zkcli` o comando para ligar com a concha apache zookeeper.

1. Correr `rmr /hbase/regions-in-transition` ou `rmr /hbase-unsecure/regions-in-transition` comandar.

1. Saia da `hbase zkcli` concha usando `exit` o comando.

1. A partir da Apache Ambari UI, reinicie o serviço Ative HBase Master.

1. Execute o comando `hbase hbck -fixAssignments`.

1. Monitorize a HBase Master UI "região em transição" nessa secção para garantir que nenhuma região fique presa.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
