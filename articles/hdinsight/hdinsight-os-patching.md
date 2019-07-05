---
title: Configurar o agendamento para clusters do HDInsight baseado em Linux - Azure da aplicação de patches de SO
description: Saiba como configurar o agendamento para clusters do HDInsight baseado em Linux a aplicação de patches de SO.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a73866a8898042b546fa47d9c3d14ab4e58e9a12
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503235"
---
# <a name="os-patching-for-hdinsight"></a>Sistema operacional a aplicação de patches para HDInsight 

> [!IMPORTANT]
> Imagens do Ubuntu tornam-se disponível para criação de cluster do HDInsight novo dentro de três meses de sua publicação. A partir de Janeiro de 2019, são clusters em execução **não** aplicação de patches automática. Os clientes tem de utilizar as ações de script ou de outros mecanismos para corrigir um cluster em execução. Clusters recém-criado sempre terá as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Como configurar o sistema operacional a aplicação de patches de agendamento para clusters do HDInsight baseado em Linux
As máquinas virtuais num cluster do HDInsight tem de ser reiniciado, ocasionalmente, para que podem ser instaladas patches de segurança importantes. 

Utilizar as ações de script descritas neste artigo, pode modificar o sistema operacional a agenda de correção da seguinte forma:
1. Instale todas as atualizações ou instalar kernel + apenas atualizações de segurança ou apenas atualizações de kernel de instalação.
2. Reinício imediato ou agenda de um reinício na VM.

> [!NOTE]  
> Estas ações de script só funciona com clusters do HDInsight baseado em Linux criados após 1 de Agosto de 2016. Patches entrarão em vigor apenas quando as VMs são reiniciadas. Estes scripts não serão aplicado automaticamente as atualizações para todos os ciclos de atualização futura. Execute os scripts que cada novas atualizações de tempo têm de ser aplicadas para instalar as atualizações e reinicie a VM.

## <a name="how-to-use-the-script"></a>Como utilizar o script 

Utilizar este script requer as seguintes informações:
1. Localização do script de instalação-atualizações-agenda-reinicializações: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight utiliza este URI para localizar e executar o script em todas as máquinas virtuais no cluster. Este script fornece opções para instalar atualizações e reinicie a VM.
  
2. Localização do script os reinícios de agenda: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight utiliza este URI para localizar e executar o script em todas as máquinas virtuais no cluster. Este script reinicia a VM.
  
3. Os tipos de nós de cluster que o script é aplicado a: nó principal, workernode, zookeeper. Este script deve ser aplicado a todos os tipos de nó do cluster. Se ele não se aplica a um tipo de nó, em seguida, as máquinas virtuais para esse tipo de nó será não ser atualizadas ou reiniciadas.

4. Parâmetro: O script de instalação-atualizações-agenda-reinicializações aceita dois parâmetros numéricos:

    | Parâmetro | Definição |
    | --- | --- |
    | Instalar apenas as atualizações de kernel / instalar todas as atualizações/instalar kernel + segurança apenas de atualizações |0 ou 1 ou 2. Um valor de 0 instala apenas atualizações de kernel ao 1 instala todas as atualizações e o kernel de 2 instalações + security atualizações apenas. Se não for fornecido nenhum parâmetro, a predefinição é 0. |
    | Sem reinício imediato do reinício/ativar agenda reinício/ativar |0 ou 1 ou 2. Um valor de 0 desativa a reinicialização, enquanto o reinício de agenda permite a 1 e 2 permite que o reinício imediato. Se não for fornecido nenhum parâmetro, a predefinição é 0. Utilizador tem 1 para 2 do parâmetro de entrada de parâmetro de entrada. |
   
 5. Parâmetro: O script de agenda reinicializações aceita um parâmetro numérico:

    | Parâmetro | Definição |
    | --- | --- |
    | Ativar o reinício imediato do reinício/ativar agenda |1 ou 2. Um valor de 1 ativa o reinício de agenda (a reinicialização é agendada de 12 a 24 horas seguintes) enquanto imediata de 2 permite reiniciar (em 5 minutos). Se não for fornecido nenhum parâmetro, a predefinição é 1. |  

> [!NOTE] 
> Tem de marcar o script como persistente ao aplicar a um cluster existente. Caso contrário, qualquer novos nós criado por meio de operações de dimensionamento irão utilizar a predefinição de agenda de correções.  Se aplicar o script como parte do processo de criação do cluster, ele é mantido automaticamente.


## <a name="next-steps"></a>Passos Seguintes

Para passos específicos sobre como utilizar a ação de script, consulte as secções seguintes [HDInsight baseado em Linux personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Utilize uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script para um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
