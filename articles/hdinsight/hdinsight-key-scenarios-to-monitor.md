---
title: Monitorar o desempenho do cluster-Azure HDInsight
description: Como monitorar a integridade e o desempenho de clusters de Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 7a7544ef9fe5724d1f6c11918411a76461d908e5
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104404"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitorar o desempenho do cluster no Azure HDInsight

Monitorar a integridade e o desempenho de um cluster HDInsight é essencial para manter o desempenho e a utilização de recursos ideais. O monitoramento também pode ajudá-lo a detectar e resolver erros de configuração de cluster e problemas de código do usuário.

As seções a seguir descrevem como monitorar e otimizar a carga em seus clusters, Apache Hadoop filas YARN e detectar problemas de limitação de armazenamento.

## <a name="monitor-cluster-load"></a>Monitorar carga do cluster

Os clusters do Hadoop podem fornecer o desempenho ideal quando a carga no cluster é distribuída uniformemente entre todos os nós. Isso permite que as tarefas de processamento sejam executadas sem restrições de RAM, CPU ou recursos de disco em nós individuais.

Para obter uma visão de alto nível dos nós do cluster e do seu carregamento, entre na [interface do usuário da Web do AmAmbari](hdinsight-hadoop-manage-ambari.md)e selecione a guia **hosts** . Seus hosts são listados por seus nomes de domínio totalmente qualificados. O status operacional de cada host é mostrado por um indicador de integridade colorido:

| Cor | Descrição |
| --- | --- |
| Vermelho | Pelo menos um componente mestre no host está inoperante. Focalize para ver uma dica de ferramenta que lista os componentes afetados. |
| Laranja | Pelo menos um componente secundário no host está inoperante. Focalize para ver uma dica de ferramenta que lista os componentes afetados. |
| Amarelo | O servidor Ambari não recebeu uma pulsação do host por mais de 3 minutos. |
| Verde | Estado de execução normal. |

Você também verá colunas que mostram o número de núcleos e a quantidade de RAM para cada host, além do uso do disco e da média de carga.

![Visão geral da guia hosts Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Selecione qualquer um dos nomes de host para obter uma visão detalhada dos componentes em execução nesse host e suas métricas. As métricas são mostradas como uma linha do tempo selecionável de uso da CPU, carga, uso do disco, uso de memória, uso da rede e números de processos.

![Visão geral dos detalhes do host Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Consulte [gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md) para obter detalhes sobre como configurar alertas e exibir métricas.

## <a name="yarn-queue-configuration"></a>Configuração de fila do YARN

O Hadoop tem vários serviços em execução em sua plataforma distribuída. O YARN (ainda outro negociador de recursos) coordena esses serviços e aloca recursos de cluster para garantir que qualquer carga seja distribuída uniformemente pelo cluster.

O YARN divide as duas responsabilidades do JobTracker, do gerenciamento de recursos e do agendamento/monitoramento de trabalhos, em dois daemons: um Gerenciador de recursos global e um aplicativo (AM) por aplicativo.

O Gerenciador de recursos é um *Agendador puro*e apenas arbitra os recursos disponíveis entre todos os aplicativos concorrentes. O Gerenciador de recursos garante que todos os recursos estejam sempre em uso, otimizando para várias constantes, como SLAs, garantias de capacidade e assim por diante. O aplicativo negocia os recursos do Gerenciador de recursos e trabalha com os NodeManager para executar e monitorar os contêineres e o consumo de recursos.

Quando vários locatários compartilham um cluster grande, há competição para os recursos do cluster. O CapacityScheduler é um Agendador conectável que auxilia no compartilhamento de recursos ao enfileirar solicitações. O CapacityScheduler também dá suporte a *filas hierárquicas* para garantir que os recursos sejam compartilhados entre as subfilas de uma organização, antes que as filas de outros aplicativos tenham permissão para usar recursos gratuitos.

O YARN nos permite alocar recursos para essas filas e mostra se todos os recursos disponíveis estão atribuídos. Para exibir informações sobre suas filas, entre na interface do usuário da Web do amAmbari e selecione **Gerenciador de filas do yarn** no menu superior.

![Gerenciador de filas do Apache Ambari YARN](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

A página Gerenciador de filas do YARN mostra uma lista de suas filas à esquerda, juntamente com a porcentagem de capacidade atribuída a cada uma.

![Página de detalhes do Gerenciador de filas do YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Para obter uma visão mais detalhada de suas filas, no painel do Ambari, selecione o serviço **yarn** na lista à esquerda. Em seguida, no menu suspenso **links rápidos** , selecione **interface do usuário do Resource Manager** abaixo do nó ativo.

![Links de menu da interface do usuário do Resource Manager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Na interface do usuário do Resource Manager, selecione **Agendador** no menu à esquerda. Você verá uma lista de suas filas sob *filas de aplicativos*. Aqui você pode ver a capacidade usada para cada uma de suas filas, o quão bem os trabalhos são distribuídos entre eles e se algum trabalho tem restrições de recursos.

![Menu da IU do Gerenciador de recursos do Apache HAdoop](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Limitação de armazenamento

O afunilamento de desempenho de um cluster pode ocorrer no nível de armazenamento. Esse tipo de afunilamento geralmente ocorre devido ao *bloqueio* de operações de e/s (entrada/saída), que ocorrem quando as tarefas em execução enviam mais e/s do que o serviço de armazenamento pode manipular. Esse bloqueio cria uma fila de solicitações de e/s aguardando para serem processadas até que o IOs atual seja processado. Os blocos são devidos à *limitação de armazenamento*, que não é um limite físico, mas sim um limite imposto pelo serviço de armazenamento por um SLA (contrato de nível de serviço). Esse limite garante que um único cliente ou locatário possa monopolizar o serviço. O SLA limita o número de IOs por segundo (IOPS) para o armazenamento do Azure-para obter detalhes, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Se você estiver usando o armazenamento do Azure, para obter informações sobre como monitorar problemas relacionados ao armazenamento, incluindo a limitação, consulte [monitorar, diagnosticar e solucionar problemas armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Se o armazenamento de backup do seu cluster for Azure Data Lake Storage (ADLS), sua limitação provavelmente ocorrerá devido a limites de largura de banda. A limitação, nesse caso, pode ser identificada observando erros de limitação nos logs de tarefas. Para ADLS, consulte a seção limitação para o serviço apropriado nestes artigos:

* [Diretrizes de ajuste de desempenho para Apache Hive no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Diretrizes de ajuste de desempenho para MapReduce no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Diretrizes de ajuste de desempenho para Apache Storm no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Passos seguintes

Visite os links a seguir para obter mais informações sobre como solucionar problemas e monitorar seus clusters:

* [Analisar registos do HDInsight](hdinsight-debug-jobs.md)
* [Depurar aplicativos com Apache Hadoop logs do YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Habilitar despejos de heap para serviços de Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
