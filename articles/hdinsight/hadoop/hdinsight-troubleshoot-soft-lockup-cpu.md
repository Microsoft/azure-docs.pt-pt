---
title: Watchdog BUG soft lockup CPU erro do cluster Azure HDInsight
description: Watchdog BUG soft lockup CPU aparece em syslogs kernel do cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 5d9d7b0fc21660dd22ff92bbe2de38c759c440ec
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944344"
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
    | Name |Correção para problema de bloqueio macio kernel |
    | URI de guião de bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo de nó(s) |Trabalhador, Zookeeper |
    | Parâmetros |N/D |

    **Selecione Persista esta ação do script...** se quiser executar o script quando forem adicionados novos nós.

1. Selecione **Criar**.

1. Espere que a execução tenha sucesso.

1. Execute a ação do script no nó de cabeça seguindo os mesmos passos do passo 3, mas desta vez com os tipos de nó: Cabeça.

1. Espere que a execução tenha sucesso.

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]