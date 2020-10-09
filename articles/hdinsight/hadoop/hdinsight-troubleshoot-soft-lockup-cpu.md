---
title: Watchdog BUG soft lockup CPU erro do cluster Azure HDInsight
description: Watchdog BUG soft lockup CPU aparece em syslogs kernel do cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894109"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Cenário: "watchdog: BUG: soft lockup - CPU" error from a Azure HDInsight cluster

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Os síslogas do núcleo contêm a mensagem de erro: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Causa

Um [inseto](https://bugzilla.kernel.org/show_bug.cgi?id=199437) em Linux Kernel está a causar bloqueios suaves do CPU.

## <a name="resolution"></a>Resolução

Aplique o patch de núcleo. O script abaixo atualiza o núcleo linux e reinicia as máquinas em diferentes momentos ao longo de 24 horas. Execute a ação do script em dois lotes. O primeiro lote está em todos os nós, exceto no nó da cabeça. O segundo lote está no nó da cabeça. Não corra sobre o nó da cabeça e outros nós ao mesmo tempo.

1. Navegue para o seu cluster HDInsight a partir do portal Azure.

1. Vai para as ações de guião.

1. Selecione **Submeter Novo** e introduzir a entrada da seguinte forma

    | Propriedade | Valor |
    | --- | --- |
    | Tipo de script | -Personalizado |
    | Nome |Correção para problema de bloqueio macio kernel |
    | URI de guião de bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo de nó(s) |Trabalhador, Zookeeper |
    | Parâmetros |N/D |

    **Selecione Persista esta ação do script...** se quiser executar o script quando forem adicionados novos nós.

1. Selecione **Criar**.

1. Espere que a execução tenha sucesso.

1. Execute a ação do script no nó de cabeça seguindo os mesmos passos do passo 3, mas desta vez com os tipos de nó: Cabeça.

1. Espere que a execução tenha sucesso.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
