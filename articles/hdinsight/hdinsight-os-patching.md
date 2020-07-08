---
title: Configurar o calendário de remendos OS para clusters Azure HDInsight
description: Aprenda a configurar o calendário de remendos de SISTEMAS para clusters HDInsight baseados em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: ddc70ccbbb5c964f16b078470517ce667bc878f1
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86082646"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configure o calendário de remendos de SISTEMA para clusters HDInsight baseados em Linux

> [!IMPORTANT]
> As imagens Ubuntu ficam disponíveis para a nova criação do cluster Azure HDInsight no prazo de três meses após a sua publicação. A partir de janeiro de 2019, os clusters de corrida não são remendados automaticamente. Os clientes devem usar ações de script ou outros mecanismos para corrigir um cluster de execução. Os clusters recém-criados terão sempre as últimas atualizações disponíveis, incluindo os mais recentes patches de segurança.

O HDInsight fornece suporte para que execute tarefas comuns no seu cluster, tais como a instalação de patches de SISTEMA, atualizações de segurança e nól de reinicialização. Estas tarefas são realizadas utilizando os seguintes dois scripts que podem ser executados como [ações de script](hdinsight-hadoop-customize-cluster-linux.md), e configurados com parâmetros:

- `schedule-reboots.sh`- Reinicie imediatamente ou agende um reinício dos nós do cluster.
- `install-updates-schedule-reboots.sh`- Instale todas as atualizações, apenas atualizações de kernel + de segurança, ou apenas atualizações de kernel.

> [!NOTE]  
> As ações do script não aplicarão automaticamente atualizações para todos os ciclos de atualização futuros. Executar os scripts cada vez que novas atualizações devem ser aplicadas para instalar as atualizações e, em seguida, reiniciar o VM.

## <a name="preparation"></a>Preparação

Remendar um ambiente representativo não produtivo antes da sua colocação na produção. Desenvolva um plano para testar adequadamente o seu sistema antes da sua remendação real.

De tempos a tempos, de uma sessão de ssh com o seu cluster, poderá receber uma mensagem de que está disponível uma atualização. A mensagem pode parecer algo como:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

Remendar é opcional e ao seu critério.

## <a name="restart-nodes"></a>Reiniciar os acenos
  
O [script-reboots](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), define o tipo de reboot que será realizado nas máquinas do cluster. Ao submeter a ação do script, desaplija-o para aplicar nos três tipos de nós: nó de cabeça, nó do trabalhador e zookeeper. Se o script não for aplicado a um tipo de nó, os VMs para esse tipo de nó não serão atualizados ou reiniciados.

O `schedule-reboots script` aceita um parâmetro numérico:

| Parâmetro | Valores aceites | Definição |
| --- | --- | --- |
| Tipo de reinício para executar | 1 ou 2 | Um valor de 1 permite o reinício do horário (programado em 12-24 horas). Um valor de 2 permite o reinício imediato (em 5 minutos). Se não for dado nenhum parâmetro, o padrão é 1. |  

## <a name="install-updates-and-restart-nodes"></a>Instale atualizações e reinicie os nosdes

O script [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) fornece opções para instalar diferentes tipos de atualizações e reiniciar o VM.

O `install-updates-schedule-reboots` guião aceita dois parâmetros numéricos, conforme descrito na tabela seguinte:

| Parâmetro | Valores aceites | Definição |
| --- | --- | --- |
| Tipo de atualizações para instalar | 0, 1 ou 2 | Um valor de 0 instala apenas atualizações de kernel. Um valor de 1 instala todas as atualizações e 2 instala apenas atualizações de kernel + segurança. Se não for fornecido nenhum parâmetro, o padrão é 0. |
| Tipo de reinício para executar | 0, 1 ou 2 | Um valor de 0 desativa o reinício. Um valor de 1 permite o reinício da programação e 2 permite o reinício imediato. Se não for fornecido nenhum parâmetro, o padrão é 0. O utilizador deve alterar o parâmetro de entrada 1 para o parâmetro de entrada 2. |

> [!NOTE]
> Deve marcar um guião como persistido depois de o aplicar a um cluster existente. Caso contrário, quaisquer novos nós criados através de operações de escala utilizarão o calendário de remendos predefinido. Se aplicar o script como parte do processo de criação de cluster, ele persiste automaticamente.

## <a name="next-steps"></a>Próximos passos

Para obter etapas específicas sobre a utilização de ações de script, consulte as seguintes secções em [conjuntos HDInsight baseados em Linux utilizando ação de script](hdinsight-hadoop-customize-cluster-linux.md):

- [Use uma ação de script durante a criação de cluster](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Aplique uma ação de script a um cluster de corrida](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
