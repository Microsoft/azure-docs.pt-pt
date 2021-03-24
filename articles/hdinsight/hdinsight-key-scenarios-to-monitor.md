---
title: Monitor cluster performance - Azure HDInsight
description: Como monitorizar a saúde e o desempenho dos clusters Apache Hadoop em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 5e6bec71c44d7fbcf2841e087af9887d99c94e6f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867581"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitor desempenho do cluster em Azure HDInsight

Monitorizar a saúde e o desempenho de um cluster HDInsight é essencial para manter o melhor desempenho e utilização de recursos. A monitorização também pode ajudá-lo a detetar e a endereçar erros de configuração do cluster e problemas de código do utilizador.

As secções seguintes descrevem como monitorizar e otimizar a carga nos seus clusters, as filas de YARN Apache Hadoop e detetar problemas de estrangulamento de armazenamento.

## <a name="monitor-cluster-load"></a>Monitor de carga de cluster

Os clusters hadoop podem proporcionar o melhor desempenho quando a carga no cluster é distribuída uniformemente por todos os nós. Isto permite que as tarefas de processamento sejam executadas sem serem limitadas por recursos de RAM, CPU ou discos em nós individuais.

Para obter uma visibilidade de alto nível para os nós do seu cluster e para o seu carregamento, inscreva-se no [Ambari Web UI,](hdinsight-hadoop-manage-ambari.md)em seguida, selecione o **separador Anfitriões.** Os seus anfitriões estão listados pelos seus nomes de domínio totalmente qualificados. O estado de funcionamento de cada hospedeiro é indicado por um indicador de saúde colorido:

| Cor | Descrição |
| --- | --- |
| Red | Pelo menos um componente principal no hospedeiro está em baixo. Hover para ver uma ponta de ferramenta que lista os componentes afetados. |
| Laranja | Pelo menos um componente secundário no hospedeiro está em baixo. Hover para ver uma ponta de ferramenta que lista os componentes afetados. |
| Amarelo | O Ambari Server não recebe batimentos cardíacos do anfitrião há mais de 3 minutos. |
| Green | Estado normal de funcionamento. |

Você também verá colunas mostrando o número de núcleos e quantidade de RAM para cada hospedeiro, e a média de utilização e carga do disco.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png" alt-text="Apache Ambari acolhe visão geral do separador":::

Selecione qualquer um dos nomes do anfitrião para uma olhada detalhada nos componentes que estão a decorrer no hospedeiro e nas suas métricas. As métricas são mostradas como uma linha temporal selecionável de utilização do CPU, carga, utilização do disco, utilização da memória, utilização da rede e número de processos.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png" alt-text="Apache Ambari acolhe visão geral dos detalhes":::

Consulte [os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) para obter detalhes sobre a definição de alertas e métricas de visualização.

## <a name="yarn-queue-configuration"></a>Configuração da fila YARN

A Hadoop tem vários serviços em toda a sua plataforma distribuída. O YARN (Mais Um Negociador de Recursos) coordena estes serviços e aloca recursos de cluster para garantir que qualquer carga seja distribuída uniformemente pelo cluster.

O YARN divide as duas responsabilidades do JobTracker, gestão de recursos e agendamento/monitorização de empregos, em dois daemons: um Gestor global de Recursos e um ApplicationMaster por aplicação (AM).

O Gestor de Recursos é um *programador puro,* e apenas arbitra os recursos disponíveis entre todas as aplicações concorrentes. O Gestor de Recursos garante que todos os recursos estão sempre em uso, otimizando para várias constantes, tais como SLAs, garantias de capacidade, etc. O ApplicationMaster negoceia recursos do Gestor de Recursos e trabalha com o NodeManager(s) para executar e monitorizar os contentores e o seu consumo de recursos.

Quando vários inquilinos partilham um grande aglomerado, há concorrência para os recursos do cluster. O CapacityScheduler é um programador pluggable que ajuda na partilha de recursos através da fila de pedidos. O CapacityScheduler também apoia *filas hierárquicas* para garantir que os recursos são partilhados entre as subques de uma organização, antes que as filas de outras aplicações sejam autorizadas a usar recursos gratuitos.

O YARN permite-nos alocar recursos a estas filas, e mostra-lhe se todos os seus recursos disponíveis estão atribuídos. Para ver informações sobre as suas filas, inscreva-se no Ambari Web UI e, em seguida, selecione **YARN Queue Manager** no menu principal.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png" alt-text="Apache Ambari YARN Gerente de Fila":::

A página do Gestor de Filas YARN mostra uma lista das suas filas à esquerda, juntamente com a percentagem de capacidade atribuída a cada uma delas.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png" alt-text="Página de detalhes do Gestor de Fila yarn":::

Para uma análise mais detalhada das suas filas, a partir do painel Ambari, selecione o serviço **YARN** da lista à esquerda. Em seguida, no menu de dropdown **de Links Rápidos,** selecione **UI do Gestor de Recursos** por baixo do nó ativo.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png" alt-text="Links de menu uI do gestor de recursos":::

Na UI do Gestor de Recursos, selecione **Scheduler** a partir do menu da mão esquerda. Vê uma lista das suas filas por baixo das *filas de aplicações.* Aqui pode ver a capacidade utilizada para cada uma das suas filas, o quão bem os postos de trabalho são distribuídos entre eles e se os empregos estão limitados aos recursos.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png" alt-text="Menu de UI do Apache HAdoop Resource Manager":::

## <a name="storage-throttling"></a>Limitação de armazenamento

O estrangulamento de desempenho de um cluster pode acontecer ao nível do armazenamento. Este tipo de estrangulamento deve-se, na maioria das vezes, *ao bloqueio* das operações de entrada/saída (IO), que acontecem quando as suas tarefas de funcionamento enviam mais IO do que o serviço de armazenamento pode suportar. Este bloqueio cria uma fila de pedidos de IO à espera de serem processados até que os IOs atuais sejam processados. Os blocos devem-se ao estrangulamento do *armazenamento*, que não é um limite físico, mas sim um limite imposto pelo serviço de armazenamento por um acordo de nível de serviço (SLA). Este limite garante que nenhum cliente ou inquilino possa monopolizar o serviço. O SLA limita o número de IOs por segundo (IOPS) para armazenamento Azure - para mais detalhes, ver [metas de escalabilidade e desempenho para contas de armazenamento padrão](../storage/common/scalability-targets-standard-account.md).

Se estiver a utilizar o Azure Storage, para obter informações sobre questões relacionadas com o armazenamento, incluindo estrangulamento, consulte [o Monitor, o diagnóstico e a resolução de problemas do Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md).

Se a loja de apoio do seu cluster for a Azure Data Lake Storage (ADLS), o seu estrangulamento é provavelmente devido aos limites de largura de banda. Neste caso, a aceleração pode ser identificada observando erros de estrangulamento nos registos de tarefas. Para a ADLS, consulte a secção de estrangulamento para o serviço adequado nestes artigos:

* [Orientação de afinação de desempenho para a Colmeia Apache em HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Orientação de afinação de desempenho para MapReduce em HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Orientação de afinação de desempenho para Apache Storm em HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Desempenho do nó lento de resolução de problemas

Em alguns casos, poderá ocorrer lentidão devido a espaço em disco reduzido no cluster. Investigue com estes passos:

1. Utilize [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para ligar a cada um dos nós.

1. Verifique a utilização do disco executando um dos seguintes comandos:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Reveja a saída e verifique a presença de ficheiros grandes na `mnt` pasta ou noutras pastas. Tipicamente, as `usercache` pastas ( `appcache` mnt/recurso/hadoop/yarn/local/usercache/hive/appcache/) contêm ficheiros grandes.

1. Se existirem ficheiros grandes, ou um trabalho atual está a causar o crescimento do ficheiro ou um trabalho anterior falhado pode ter contribuído para este problema. Para verificar se este comportamento é causado por um trabalho atual, execute o seguinte comando: 

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Se este comando indicar um trabalho específico, pode optar por encerrar o trabalho utilizando um comando que se assemelhe ao seguinte:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    `application_id`Substitua-o pelo ID da aplicação. Se não forem indicadas tarefas especificas, avance para o passo seguinte.

1. Após o comando acima concluído, ou se não forem indicados trabalhos específicos, elimine os grandes ficheiros identificados executando um comando que se assemelha ao seguinte:

    ```bash
    rm -rf filecache usercache
    ```

Para obter mais informações sobre problemas no espaço do disco, consulte [fora do espaço do disco.](./hadoop/hdinsight-troubleshoot-out-disk-space.md)

> [!NOTE]  
> Se tiver ficheiros grandes que pretende manter, mas que estão a contribuir para o problema de espaço em disco baixo, tem de aumentar o seu cluster HDInsight e reiniciar os seus serviços. Depois de completar este procedimento e esperar por alguns minutos, você vai notar que o armazenamento é libertado e o desempenho habitual do nó é restaurado.

## <a name="next-steps"></a>Passos seguintes

Visite os seguintes links para obter mais informações sobre a resolução de problemas e monitorização dos seus clusters:

* [Analisar registos do HDInsight](./hdinsight-troubleshoot-guide.md)
* [Aplicativos de depurar com troncos Apache Hadoop YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Ativar depósitos de pilhas para serviços Apache Hadoop em HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)