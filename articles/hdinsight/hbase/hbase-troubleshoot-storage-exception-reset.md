---
title: Exceção de armazenamento após a redefinição de conexão no Azure HDInsight
description: Exceção de armazenamento após a redefinição de conexão no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887228"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Cenário: exceção de armazenamento após a redefinição de conexão no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível criar uma nova tabela do Apache HBase.

## <a name="cause"></a>Causa

Durante um processo de truncamento de tabela, houve um problema de conexão de armazenamento. A entrada da tabela foi excluída na tabela de metadados do HBase. Todos, exceto um arquivo de BLOB, foram excluídos.

Embora não haja nenhum blob de pasta chamado `/hbase/data/default/ThatTable` sentado no armazenamento. O driver WASB encontrou a existência do arquivo de blob acima e não permitiria criar nenhum blob chamado `/hbase/data/default/ThatTable` porque ele assumiu que as pastas pai existiam, portanto, a criação da tabela falhará.

## <a name="resolution"></a>Resolução

1. Na interface do usuário do Apache Ambari, reinicie o HMaster ativo. Isso permitirá que um dos dois HMaster em espera se torne o ativo e o novo HMaster ativo recarregará as informações da tabela de metadados. Portanto, você não verá a tabela `already-deleted` na interface do usuário do HMaster.

1. Você pode encontrar o arquivo de blob órfão em ferramentas de interface do usuário, como o Cloud Explorer, ou executar comando como `hdfs dfs -ls /xxxxxx/yyyyy`. Execute `hdfs dfs -rmr /xxxxx/yyyy` para excluir esse BLOB. Por exemplo, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Agora você pode criar uma nova tabela com o mesmo nome no HBase.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
