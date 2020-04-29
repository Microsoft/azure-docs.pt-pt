---
title: Configure o calendário de patching do OS para clusters Azure HDInsight
description: Saiba como configurar o calendário de correção de OS para clusters HDInsight baseados em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78206865"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configure o calendário de correção de OS para clusters HDInsight baseados em Linux

> [!IMPORTANT]
> As imagens Ubuntu ficam disponíveis para a criação de novos clusters Azure HDInsight no prazo de três meses após a publicação. A partir de janeiro de 2019, os clusters de corrida não são auto-remendados. Os clientes devem usar ações de script ou outros mecanismos para remendar um cluster de execução. Os clusters recém-criados terão sempre as mais recentes atualizações disponíveis, incluindo as mais recentes correções de segurança.

O HDInsight fornece suporte para que execute tarefas comuns no seu cluster, tais como instalar patches de OS, atualizações de segurança e reinicialização de nós. Estas tarefas são realizadas usando os seguintes dois scripts que podem ser executados como ações de [script](hdinsight-hadoop-customize-cluster-linux.md), e configurados com parâmetros:

- `schedule-reboots.sh`- Faça um reinício imediato ou agende um reinício dos nós do cluster.
- `install-updates-schedule-reboots.sh`- Instale todas as atualizações, apenas atualizações de kernel + segurança, ou apenas atualizações de kernel.

> [!NOTE]  
> As ações do Script não aplicarão automaticamente atualizações para todos os ciclos de atualização futuros. Executar os scripts sempre que novas atualizações devem ser aplicadas para instalar as atualizações e, em seguida, reiniciar o VM.

## <a name="preparation"></a>Preparação

Patch em um ambiente representativo de não produção antes de ser implantado para a produção. Desenvolva um plano para testar adequadamente o seu sistema antes da sua correção real.

De tempos a tempos, a partir de uma sessão de SSH com o seu cluster, poderá receber uma mensagem de que está disponível uma atualização. A mensagem pode parecer algo como:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

Remendar é opcional e a seu critério.

## <a name="restart-nodes"></a>Reinicie os nódosos
  
O [script schedule-reboots,](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)define o tipo de reboot que será realizado nas máquinas do cluster. Ao submeter a ação do guião, deite-o para aplicar nos três tipos de nó: nó de cabeça, nó de trabalhador e zookeeper. Se o script não for aplicado a um nó, os VMs para esse tipo de nó não serão atualizados ou reiniciados.

O `schedule-reboots script` aceita um parâmetro numérico:

| Parâmetro | Valores aceites | Definição |
| --- | --- | --- |
| Tipo de reinício a executar | 1 ou 2 | Um valor de 1 permite o reinício do horário (programado em 12-24 horas). Um valor de 2 permite o reinício imediato (em 5 minutos). Se não for dado nenhum parâmetro, o padrão é 1. |  

## <a name="install-updates-and-restart-nodes"></a>Instale atualizações e reinicie os nódosos

O script [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) fornece opções para instalar diferentes tipos de atualizações e reiniciar o VM.

O `install-updates-schedule-reboots` guião aceita dois parâmetros numéricos, conforme descrito na tabela seguinte:

| Parâmetro | Valores aceites | Definição |
| --- | --- | --- |
| Tipo de atualizações para instalar | 0, 1 ou 2 | Um valor de 0 instala apenas atualizações de kernel. Um valor de 1 instala todas as atualizações e 2 instala apenas atualizações kernel + segurança. Se não for fornecido nenhum parâmetro, o padrão é de 0. |
| Tipo de reinício a executar | 0, 1 ou 2 | Um valor de 0 desativa reinicia. Um valor de 1 permite o reinício do horário e 2 permite o reinício imediato. Se não for fornecido nenhum parâmetro, o padrão é de 0. O utilizador deve alterar o parâmetro de entrada 1 para o parâmetro de entrada 2. |

> [!NOTE]
> Deve marcar um guião como persistido depois de o aplicar a um aglomerado existente. Caso contrário, quaisquer novos nós criados através de operações de escala utilizarão o calendário de correção predefinido. Se aplicar o script como parte do processo de criação do cluster, persiste automaticamente.

## <a name="next-steps"></a>Passos seguintes

Para passos específicos sobre a utilização de ações de script, consulte as seguintes secções em [clusters HDInsight baseados em Linux, utilizando a ação](hdinsight-hadoop-customize-cluster-linux.md)do script:

- [Use uma ação de script durante a criação de cluster](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Aplicar uma ação de script a um cluster de execução](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
