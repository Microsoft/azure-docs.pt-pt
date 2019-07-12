---
title: Configurar o sistema operacional a aplicação de patches de agendamento para clusters do HDInsight baseado em Linux - Azure
description: Saiba como configurar o agendamento para clusters do HDInsight baseado em Linux a aplicação de patches de SO.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657058"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurar o sistema operacional a aplicação de patches de agendamento para clusters do HDInsight baseado em Linux 

> [!IMPORTANT]
> Imagens do Ubuntu tornam-se disponível para criação de cluster do HDInsight do Azure novo dentro de três meses de sua publicação. A partir de Janeiro de 2019 clusters em execução não são automaticamente correções. Os clientes tem de utilizar as ações de script ou de outros mecanismos para corrigir um cluster em execução. Clusters recém-criado sempre terá as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

Ocasionalmente, tem de reiniciar as máquinas virtuais (VMs) num cluster do HDInsight para instalar os patches de segurança importantes.

Ao utilizar as ações de script descritas neste artigo, pode modificar o sistema operacional a agenda de correção da seguinte forma:

1. Instale todas as atualizações, ou instale apenas kernel + atualizações de segurança ou atualizações de kernel.
2. Fazer um reinício imediato ou agendar um reinício na VM.

> [!NOTE]  
> As ações de script descritas neste artigo funcionará apenas com clusters do HDInsight baseado em Linux criados após 1 de Agosto de 2016. Patches entram em vigor apenas após o reinício de VMs.
> Ações de script não aplicam automaticamente as atualizações para todos os ciclos de atualização futura. Execute os scripts sempre que novas atualizações devem ser aplicadas para instalar as atualizações e, em seguida, reinicie a VM.

## <a name="add-information-to-the-script"></a>Adicionar informações ao script

Usando um script requer as seguintes informações:

- Localização do script de instalação-atualizações-agenda-reinicializações: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight utiliza este URI para localizar e executar o script em todas as VMs no cluster. Este script fornece opções para instalar atualizações e reinicie a VM.
  
- Localização do script os reinícios de agenda: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight utiliza este URI para localizar e executar o script em todas as VMs no cluster. Este script reinicia a VM.
  
- Os tipos de nós de cluster que o script é aplicado a são o nó principal, workernode e zookeeper. Aplica o script a todos os tipos de nó do cluster. Se o script não é aplicado a um tipo de nó, as VMs para esse tipo de nó não ser atualizadas ou reiniciadas.

- O script de instalação-atualizações-agenda-reinicializações aceita dois parâmetros numéricos:

    | Parâmetro | Definição |
    | --- | --- |
    | Instalar apenas as atualizações de kernel / instalar todas as atualizações/instalar kernel + segurança apenas de atualizações|0, 1 ou 2. Um valor de 0 instala apenas as atualizações de kernel. Um valor de 1 instala todas as atualizações e o kernel de apenas 2 instalações + atualizações de segurança. Se não for fornecido nenhum parâmetro, a predefinição é 0. |
    | Sem reinício imediato do reinício/ativar agenda reinício/ativar |0, 1 ou 2. Um valor de 0 desativa o reinício. Um valor de 1 ativa o reinício de agenda e 2 permite que o reinício de imediato. Se não for fornecido nenhum parâmetro, a predefinição é 0. O utilizador tem de alterar o parâmetro de entrada 1 para 2 do parâmetro de entrada. |
   
 - O script de agenda reinicializações aceita um parâmetro numérico:

    | Parâmetro | Definição |
    | --- | --- |
    | Ativar o reinício imediato do reinício/ativar agenda |1 ou 2. Um valor de 1 ativa o reinício de agenda (agendado em 12 a 24 horas). Um valor de 2 permite que o reinício imediato (em 5 minutos). Se nenhum parâmetro é fornecido, a predefinição é 1. |  

> [!NOTE]
> Tem de marcar um script como persistentes depois de aplicar a um cluster existente. Caso contrário, qualquer novos nós criado por meio de operações de dimensionamento irão utilizar a predefinição de agenda de correções. Se aplicar o script como parte do processo de criação do cluster, ele se manteve automaticamente.


## <a name="next-steps"></a>Passos Seguintes

Para passos específicos sobre como utilizar as ações de script, consulte as secções seguintes [HDInsight baseado em Linux personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Utilize uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script para um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
