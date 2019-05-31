---
title: Configurar o agendamento para clusters do HDInsight baseado em Linux - Azure da aplicação de patches de SO
description: Saiba como configurar o agendamento para clusters do HDInsight baseado em Linux a aplicação de patches de SO.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: cfbd68e66730fc338130bc16849fe0b2f4abd6be
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244416"
---
# <a name="os-patching-for-hdinsight"></a>Sistema operacional a aplicação de patches para HDInsight 

> [!IMPORTANT]
> Imagens do Ubuntu tornam-se disponível para criação de cluster do HDInsight novo dentro de 3 meses de ser publicada. A partir de Janeiro de 2019, são clusters em execução **não** aplicação de patches automática. Os clientes tem de utilizar as ações de script ou de outros mecanismos para corrigir um cluster em execução. Clusters recém-criado sempre terá as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Como configurar o sistema operacional a aplicação de patches de agendamento para clusters do HDInsight baseado em Linux
As máquinas virtuais num cluster do HDInsight tem de ser reiniciado, ocasionalmente, para que podem ser instaladas patches de segurança importantes. 

Com a ação de script descrita neste artigo, pode modificar o sistema operacional a agenda de correção da seguinte forma:
1. Instalar as atualizações do SO completas ou instalar apenas as atualizações de segurança
2. Reinicie a VM

> [!NOTE]  
> Esta ação de script só funciona com clusters do HDInsight baseado em Linux criados após 1 de Agosto de 2016. Patches entrarão em vigor apenas quando as VMs são reiniciadas. Este script não será aplicado automaticamente as atualizações para todos os ciclos de atualização futura. Execute o script a que cada novas atualizações de tempo têm de ser aplicadas para instalar as atualizações e reinicie a VM.

## <a name="how-to-use-the-script"></a>Como utilizar o script 

Quando utilizar este script requer as seguintes informações:
1. A localização do script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh.  HDInsight utiliza este URI para localizar e executar o script em todas as máquinas virtuais no cluster.
  
2. Os tipos de nós de cluster que o script é aplicado a: nó principal, workernode, zookeeper. Este script deve ser aplicado a todos os tipos de nó do cluster. Se ele não se aplica a um tipo de nó, em seguida, as máquinas virtuais para esse tipo de nó não serão atualizadas.


3.  Parâmetro: Este script aceita um parâmetro numérico:

    | Parâmetro | Definição |
    | --- | --- |
    | Atualizações do SO completo de instalação/instalar apenas as atualizações de segurança |0 ou 1. Um valor de 0 instala atualizações de segurança apenas enquanto 1 instala as atualizações do SO completas. Se não for fornecido nenhum parâmetro, que a predefinição é 0. |

> [!NOTE]  
> Tem de marcar esse script como persistente ao aplicar a um cluster existente. Caso contrário, qualquer novos nós criado por meio de operações de dimensionamento irão utilizar a predefinição de agenda de correções.  Se aplicar o script como parte do processo de criação do cluster, ele é mantido automaticamente.


## <a name="next-steps"></a>Passos Seguintes

Para passos específicos sobre como utilizar a ação de script, consulte as secções seguintes a [HDInsight baseado em Linux personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Utilize uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script para um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
