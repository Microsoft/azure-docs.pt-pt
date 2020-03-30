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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887194"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Cenário: Intervalos com comando 'hbase hbck' em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Encontre intervalos `hbase hbck` com comando ao fixar tarefas da região.

## <a name="cause"></a>Causa

Uma potencial causa para problemas de `hbck` tempo quando se usa o comando pode ser que várias regiões estejam em "transição" durante muito tempo. Pode ver essas regiões como offline na HBase Master UI. Como um elevado número de regiões estão a tentar fazer a transição, o HBase Master pode ter um tempo e ser incapaz de voltar a pôr essas regiões online.

## <a name="resolution"></a>Resolução

1. Inscreva-se no cluster HDInsight HBase utilizando o SSH.

1. Executar `hbase zkcli` comando para ligar com a concha apache zookeeper.

1. Correr `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` ou comandar.

1. Saia `hbase zkcli` da concha `exit` usando o comando.

1. A partir do Apache Ambari UI, reinicie o serviço Ative HBase Master.

1. Execute o comando `hbase hbck -fixAssignments`.

1. Monitorize a HBase Master UI "região em transição" essa secção para garantir que nenhuma região fique presa.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
