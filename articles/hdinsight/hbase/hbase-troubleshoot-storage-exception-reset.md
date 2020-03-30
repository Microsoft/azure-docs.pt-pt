---
title: Exceção de armazenamento após reset de ligação em Azure HDInsight
description: Exceção de armazenamento após reset de ligação em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887228"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Cenário: Exceção de armazenamento após reset de ligação no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Incapaz de criar uma nova tabela Apache HBase.

## <a name="cause"></a>Causa

Durante um processo de truncação de mesa, houve um problema de ligação de armazenamento. A entrada da tabela foi eliminada na tabela de metadados HBase. Todos menos um ficheiro blob foi apagado.

Embora não houvesse uma `/hbase/data/default/ThatTable` bolha de pasta chamada sentada no armazém. O condutor do WASB encontrou a existência do ficheiro blob acima `/hbase/data/default/ThatTable` e não permitiu criar qualquer bolha chamada porque assumiu que as pastas-mãe existiam, criando assim tabela falharia.

## <a name="resolution"></a>Resolução

1. Da Apache Ambari UI, reinicie o HMaster ativo. Isto permitirá que um dos dois HMaster de standby se torne o ativo e o novo HMaster ativo irá recarregar a informação da tabela de metadados. Assim, não verá `already-deleted` a mesa na HMaster UI.

1. Você pode encontrar o ficheiro de blob órfão de `hdfs dfs -ls /xxxxxx/yyyyy`ferramentas UI como Cloud Explorer ou comando de execução como . Corra `hdfs dfs -rmr /xxxxx/yyyy` para apagar a bolha. Por exemplo, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Agora pode criar uma nova tabela com o mesmo nome em HBase.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
