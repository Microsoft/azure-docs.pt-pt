---
title: Exceção de armazenamento após a redefinição de conexão no Azure HDInsight
description: Exceção de armazenamento após a redefinição de conexão no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 52135391024eafdfea15afd6c05a5d13bf92a2c7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091574"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Cenário: Exceção de armazenamento após a redefinição de conexão no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível criar uma nova tabela do Apache HBase.

## <a name="cause"></a>Causa

Durante um processo de truncamento de tabela, houve um problema de conexão de armazenamento. A entrada da tabela foi excluída na tabela de metadados do HBase. Todos, exceto um arquivo de BLOB, foram excluídos.

Embora não haja nenhum blob de pasta `/hbase/data/default/ThatTable` chamado sentado no armazenamento. O driver WASB encontrou a existência do arquivo de blob acima e não permitiria criar nenhum blob chamado `/hbase/data/default/ThatTable` porque ele assumiu que as pastas pai existiam, portanto, a criação da tabela falhará.

## <a name="resolution"></a>Resolução

1. Na interface do usuário do Apache Ambari, reinicie o HMaster ativo. Isso permitirá que um dos dois HMaster em espera se torne o ativo e o novo HMaster ativo recarregará as informações da tabela de metadados. Portanto, você não verá a `already-deleted` tabela na interface do usuário do amHMaster.

1. Você pode encontrar o arquivo de blob órfão nas ferramentas de interface do usuário, como `hdfs dfs -ls /xxxxxx/yyyyy`o Cloud Explorer ou executando o comando como. Execute `hdfs dfs -rmr /xxxxx/yyyy` para excluir esse BLOB. Por exemplo, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Agora você pode criar uma nova tabela com o mesmo nome no HBase.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
