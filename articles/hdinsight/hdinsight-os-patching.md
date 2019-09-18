---
title: Configurar o agendamento de aplicação de patch do sistema operacional para clusters HDInsight baseados em Linux – Azure
description: Saiba como configurar o agendamento de aplicação de patch de so para clusters HDInsight baseados em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 06111ec35a127cf17fdcc77ff717de7a4bc7299f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076865"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurar o agendamento de aplicação de patch do so para clusters HDInsight baseados em Linux 

> [!IMPORTANT]
> As imagens do Ubuntu são disponibilizadas para a nova criação de cluster do Azure HDInsight dentro de três meses após a publicação. A partir de janeiro de 2019, os clusters em execução não são automaticamente corrigidos. Os clientes devem usar ações de script ou outros mecanismos para aplicar patch em um cluster em execução. Os clusters recém-criados sempre terão as atualizações mais recentes disponíveis, incluindo os patches de segurança mais recentes.

Ocasionalmente, você deve reiniciar máquinas virtuais (VMs) em um cluster HDInsight para instalar patches de segurança importantes.

Usando as ações de script descritas neste artigo, você pode modificar a agenda de aplicação de patch do sistema operacional da seguinte maneira:

1. Instale todas as atualizações ou instale apenas as atualizações do kernel + segurança ou atualizações de kernel.
2. Faça uma reinicialização imediata ou agende uma reinicialização na VM.

> [!NOTE]  
> As ações de script descritas neste artigo funcionarão somente com clusters HDInsight baseados em Linux criados após 1º de agosto de 2016. Os patches entram em vigor somente após a reinicialização das VMs.
> As ações de script não aplicarão atualizações automaticamente para todos os ciclos de atualização futuros. Execute os scripts sempre que novas atualizações devem ser aplicadas para instalar as atualizações e reinicie a VM.

## <a name="add-information-to-the-script"></a>Adicionar informações ao script

O uso de um script requer as seguintes informações:

- O local do script install-updates-agenda-reinicializações: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   O HDInsight usa esse URI para localizar e executar o script em todas as VMs no cluster. Esse script fornece opções para instalar atualizações e reiniciar a VM.
  
- O local do script agenda-reinicializações https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh:.
    
   O HDInsight usa esse URI para localizar e executar o script em todas as VMs no cluster. Esse script reinicia a VM.
  
- Os tipos de nó de cluster aos quais o script é aplicado são cabeçalho, workernode e Zookeeper. Aplique o script a todos os tipos de nó no cluster. Se o script não for aplicado a um tipo de nó, as VMs desse tipo de nó não serão atualizadas ou reiniciadas.

- O script install-updates-agenda-reinicializações aceita dois parâmetros numéricos:

    | Parâmetro | Definição |
    | --- | --- |
    | Instalar somente atualizações de kernel/instalar todas as atualizações/instalar apenas o kernel + atualizações de segurança|0, 1 ou 2. O valor 0 instala apenas as atualizações do kernel. Um valor de 1 instala todas as atualizações e 2 instala apenas as atualizações de kernel + segurança. Se nenhum parâmetro for fornecido, o padrão será 0. |
    | Sem reinicialização/habilitação da agenda/habilitação da reinicialização imediata |0, 1 ou 2. Um valor de 0 desabilita A reinicialização. Um valor de 1 Habilita A reinicialização da agenda e 2 habilita a reinicialização imediata. Se nenhum parâmetro for fornecido, o padrão será 0. O usuário deve alterar o parâmetro de entrada 1 para o parâmetro de entrada 2. |
   
 - O script agenda-reinicializações aceita um parâmetro numérico:

    | Parâmetro | Definição |
    | --- | --- |
    | Habilitar reinicialização de agendamento/habilitar reinicialização imediata |1 ou 2. Um valor de 1 Habilita A reinicialização da agenda (agendada em 12-24 horas). Um valor de 2 habilita A reinicialização imediata (em 5 minutos). Se nenhum parâmetro for fornecido, o padrão será 1. |  

> [!NOTE]
> Você deve marcar um script como persistente depois de aplicá-lo a um cluster existente. Caso contrário, todos os novos nós criados por meio de operações de dimensionamento usarão o agendamento de aplicação de patch padrão. Se você aplicar o script como parte do processo de criação do cluster, ele será persistido automaticamente.


## <a name="next-steps"></a>Passos Seguintes

Para obter etapas específicas sobre como usar as ações de script, consulte as seções a seguir em [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usar uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script a um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
