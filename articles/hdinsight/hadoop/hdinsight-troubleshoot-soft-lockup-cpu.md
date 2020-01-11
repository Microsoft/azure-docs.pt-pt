---
title: Erro de CPU de travamento suave do Watchdog BUG do cluster HDInsight do Azure
description: CPU de travamento soft do BUG do Watchdog aparece em syslogs de kernel do cluster HDInsight do Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894109"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Cenário: erro "Watchdog: BUG: bloqueio Soft-CPU" de um cluster HDInsight do Azure

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Os syslogs de kernel contêm a mensagem de erro: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Causa

Um [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) no kernel do Linux está causando travamentos de CPU.

## <a name="resolution"></a>Resolução

Aplique o patch do kernel. O script abaixo atualiza o kernel do Linux e reinicia as máquinas em momentos diferentes em 24 horas. Execute a ação de script em dois lotes. O primeiro lote está em todos os nós, exceto no nó de cabeçalho. O segundo lote está no nó principal. Não execute no nó principal e em outros nós ao mesmo tempo.

1. Navegue até o cluster HDInsight do portal do Azure.

1. Vá para ações de script.

1. Selecione **Enviar novo** e insira a entrada da seguinte maneira

    | Propriedade | Valor |
    | --- | --- |
    | Tipo de script | -Personalizado |
    | Nome |Correção para o problema de bloqueio soft do kernel |
    | URI do script bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo (s) de nó |Trabalho, Zookeeper |
    | Parâmetros |N/A |

    Selecione **persistir esta ação de script...** se você quiser executar o script quando novos nós forem adicionados.

1. Selecione **Criar**.

1. Aguarde até que a execução seja realizada com sucesso.

1. Execute a ação de script no nó de cabeçalho seguindo as mesmas etapas da etapa 3, mas desta vez com os tipos de nó: Head.

1. Aguarde até que a execução seja realizada com sucesso.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
