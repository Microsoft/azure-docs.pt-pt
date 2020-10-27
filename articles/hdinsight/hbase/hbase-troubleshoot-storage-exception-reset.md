---
title: Exceção de armazenamento após reposição de ligação em Azure HDInsight
description: Exceção de armazenamento após reposição de ligação em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 82fb0e0ae5722f972cdfe90581c96df2a61f0124
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539960"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Cenário: Exceção de armazenamento após reposição de ligação no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Incapaz de criar nova tabela Apache HBase.

## <a name="cause"></a>Causa

Durante um processo de truncação de mesa, houve um problema de ligação de armazenamento. A entrada da tabela foi eliminada na tabela de metadados HBase. Todos menos um ficheiro blob foi apagado.

Embora não houvesse uma bolha de pasta chamada `/hbase/data/default/ThatTable` sentada no armazenamento. O controlador WASB encontrou a existência do ficheiro blob acima e não permitiria criar qualquer bolha chamada `/hbase/data/default/ThatTable` porque assumia que as pastas dos pais existiam, criando assim a tabela falhará.

## <a name="resolution"></a>Resolução

1. A partir da Apache Ambari UI, reinicie o HMaster ativo. Isto permitirá que um dos dois HMaster de espera se torne o ativo e o novo HMaster ativo recarregará a informação da tabela de metadados. Assim, não verá a `already-deleted` mesa na UI HMaster.

1. Pode encontrar o ficheiro blob órfão a partir de ferramentas de UI como o Cloud Explorer ou o comando de execução como `hdfs dfs -ls /xxxxxx/yyyyy` . Corra `hdfs dfs -rmr /xxxxx/yyyy` para apagar a bolha. Por exemplo, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Agora pode criar uma nova tabela com o mesmo nome na Base H.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).