---
title: Monitorizar o desempenho do cluster - Azure HDInsight
description: Como monitorizar um cluster do HDInsight para a capacidade e desempenho.
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: arindamc
ms.openlocfilehash: 9a6a63748ef36bbbceb00bc815616f2cb12692a7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799797"
---
# <a name="monitor-cluster-performance"></a>Monitorizar o desempenho do cluster

Monitorizar o estado de funcionamento e o desempenho de um cluster do HDInsight é essencial para manter a otimizar o desempenho e utilização de recursos. Monitorização pode também ajudar a detetar e resolver problemas de código de utilizador e de erros de configuração de cluster.

As secções seguintes descrevem como monitorizar e otimizar a carga nos seus clusters, filas de Apache Hadoop YARN e detetar problemas de limitação de armazenamento.

## <a name="monitor-cluster-load"></a>Carga de cluster de monitor

Clusters do Hadoop podem fornecer um desempenho mais otimizado, quando a carga no cluster é distribuída uniformemente entre todos os nós. Isto permite que as tarefas de processamento para execução sem que sejam restringidos pela RAM, CPU ou recursos de disco em nós individuais.

Para obter uma visão de alto nível os nós do cluster e o carregamento, inicie sessão para o [IU Web do Ambari](hdinsight-hadoop-manage-ambari.md), em seguida, selecione a **anfitriões** separador. Os anfitriões estão listados por seus nomes de domínio completamente qualificado. Estado de funcionamento de cada anfitrião é apresentado por um indicador de estado de funcionamento coloridos:

| Cor | Descrição |
| --- | --- |
| Vermelho | Pelo menos um componente principal do anfitrião está inativo. Passe o Mouse para ver uma descrição que listas afetados componentes. |
| Laranja | Pelo menos um componente secundário no anfitrião está inativo. Passe o Mouse para ver uma descrição que listas afetados componentes. |
| Amarelo | Servidor Ambari não ter recebido um heartbeat do anfitrião durante mais de 3 minutos. |
| Verde | Estado de funcionamento do normal. |

Também verá colunas que mostra o número de núcleos e a quantidade de RAM para cada anfitrião e a utilização do disco e a carga média.

![Separador anfitriões](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Selecione qualquer um dos nomes de anfitrião para uma visão detalhada de componentes em execução no anfitrião e as suas métricas. As métricas são apresentadas como uma linha cronológica selecionável de utilização, o carga de CPU, utilização do disco, a utilização de memória, utilização de rede e um número de processos.

![Detalhes do anfitrião](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Ver [clusters do HDInsight gerir com a IU do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md) para obter detalhes sobre como alertas e ver métricas.

## <a name="yarn-queue-configuration"></a>Configuração da fila de YARN

Hadoop tem vários serviços em execução na sua plataforma distribuída. YARN (ainda outro recurso Negociador) coordena estes serviços e aloca recursos de cluster para se certificar de que qualquer carga é distribuída uniformemente no cluster.

YARN divide as duas responsabilidades do JobTracker, a gestão de recursos e o trabalho de agendamento/monitorização, em dois daemons: um Gerenciador de recursos global e um ApplicationMaster por aplicação (AM).

O Gestor de recursos é um *agendador puro*e apenas arbitrates recursos disponíveis entre todos os aplicativos concorrentes. O Gestor de recursos assegura que todos os recursos estejam sempre em utilização, otimizar para vários constantes, como o SLA, garante a capacidade e assim por diante. O ApplicationMaster negocia recursos do Resource Manager e funciona com o NodeManager(s) para executar e monitorizar os contentores e seu consumo de recursos.

Quando vários inquilinos partilham um cluster grande, não há concorrência de recursos do cluster. O CapacityScheduler é um agendador conectável, que ajuda a colocação em fila a pedidos de partilha de recursos. Também suporta o CapacityScheduler *filas hierárquicas* para garantir que os recursos são compartilhados entre as filas de frações de uma organização, antes de filas dos outros aplicativos têm permissão para utilizar recursos gratuitos.

YARN permite-nos alocar recursos para estas filas e mostra-lhe se todos os seus recursos disponíveis estão atribuídos. Para ver informações sobre as filas, inicie sessão na IU da Web do Ambari, em seguida, selecione **Gestor de filas do YARN** no menu superior.

![Gestor de filas de YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

A página de Gestor de filas do YARN mostra uma lista das suas filas no lado esquerdo, juntamente com a percentagem da capacidade atribuída a cada um.

![Página de detalhes do Gestor de filas de YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Para uma visão mais detalhada de suas filas, a partir do dashboard de Ambari, selecione o **YARN** serviço a partir da lista à esquerda. Em seguida, sob o **ligações rápidas** menu pendente, selecione **da IU do Gestor de recursos** sob o nó ativo.

![Ligação de menu da IU do Gestor de recursos](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Na IU do Gestor de recursos, selecione **agendador** no menu esquerdo. É apresentada uma lista das suas filas por baixo *filas de aplicativo*. Aqui pode ver a capacidade utilizada para cada uma das suas filas, bem como as tarefas são distribuídas entre eles, e se quaisquer tarefas estão a restrição de recursos.

![Ligação de menu da IU do Gestor de recursos](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Otimização do armazenamento

Afunilamento de desempenho de um cluster pode acontecer ao nível do armazenamento. Este tipo de estrangulamento com mais freqüência é devido a *bloqueio* operações (e/s), o que acontecem quando as tarefas em execução enviar mais e/s do que o serviço de armazenamento pode processar de entrada/saída. Este bloqueio cria uma fila de pedidos de e/s a aguardar processamento até depois de IOs atuais são processados. Os blocos são devido a *otimização de armazenamento*, que não é um limite físico, mas em vez disso, um limite imposto pelo serviço de armazenamento por um contrato de nível de serviço (SLA). Este limite assegura que nenhum inquilino ou cliente único pode monopolizar o serviço. O SLA limita o número do IOs por segundo (IOPS) para o armazenamento do Azure - para obter detalhes, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Se estiver a utilizar o armazenamento do Azure, para obter informações sobre problemas relacionados com o armazenamento de monitorização, incluindo limitação, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Se o armazenamento de backup do seu cluster do Azure Data Lake Storage (ADLS), a limitação é mais provável devido a limites de largura de banda. Limitação nesse caso foi identificada por erros de limitação a observar nos registos de tarefas. Para ADLS, consulte a secção de limitação para o serviço apropriado nestes artigos:

* [Guia para o Apache Hive no HDInsight e o armazenamento do Azure Data Lake de sintonização de desempenho](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Guia para o MapReduce no HDInsight e o armazenamento do Azure Data Lake de sintonização de desempenho](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Guia para Apache Storm no HDInsight e o armazenamento do Azure Data Lake de sintonização de desempenho](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Passos Seguintes

Visite as seguintes ligações para mais informações sobre os clusters de monitorização e resolução de problemas:

* [Analisar registos do HDInsight](hdinsight-debug-jobs.md)
* [Depurar aplicações com registos YARN do Apache Hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Ativar capturas de área dinâmica para dados dos serviços de Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
