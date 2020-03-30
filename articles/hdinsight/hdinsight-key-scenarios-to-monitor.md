---
title: Monitor desempenho do cluster - Azure HDInsight
description: Como monitorizar a saúde e o desempenho dos clusters Apache Hadoop em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082885"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitor desempenho do cluster em Azure HDInsight

Monitorizar a saúde e o desempenho de um cluster HDInsight é essencial para manter o desempenho e a utilização de recursos ideais. A monitorização também pode ajudá-lo a detetar e a resolver erros de configuração do cluster e problemas de código do utilizador.

As seguintes secções descrevem como monitorizar e otimizar a carga nos seus clusters, as filas de YARN Apache Hadoop e detetar problemas de estrangulamento de armazenamento.

## <a name="monitor-cluster-load"></a>Monitorizar a carga do cluster

Os clusters hadoop podem proporcionar o desempenho mais ideal quando a carga no cluster é distribuída uniformemente por todos os nós. Isto permite que as tarefas de processamento sejam executadas sem serem limitadas por RAM, CPU ou recursos de disco em nós individuais.

Para obter um olhar de alto nível para os nós do seu cluster e seu carregamento, inscreva-se no [Ambari Web UI,](hdinsight-hadoop-manage-ambari.md)em seguida, selecione o separador **Anfitriões.** Os seus anfitriões estão listados pelos seus nomes de domínio totalmente qualificados. O estado de funcionamento de cada hospedeiro é mostrado por um indicador de saúde colorido:

| Cor | Descrição |
| --- | --- |
| Vermelho | Pelo menos um componente principal no hospedeiro está em baixo. Passe para ver uma dica de ferramenta que lista os componentes afetados. |
| Orange | Pelo menos um componente secundário no hospedeiro está em baixo. Passe para ver uma dica de ferramenta que lista os componentes afetados. |
| Amarelo | Ambari Server não recebe batimentos cardíacos do hospedeiro há mais de 3 minutos. |
| Verde | Estado normal de execução. |

Você também verá colunas mostrando o número de núcleos e quantidade de RAM para cada anfitrião, e o uso do disco e a média de carga.

![Apache Ambari acolhe visão geral do separador](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Selecione qualquer um dos nomes do anfitrião para uma análise detalhada dos componentes que estão em execução nesse hospedeiro e nas suas métricas. As métricas são mostradas como uma linha temporal selecionável de utilização de CPU, carga, utilização do disco, utilização da memória, utilização da rede e número seletiva de processos.

![Apache Ambari acolhe detalhes da visão geral](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Consulte [os clusters Manage HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) para obter detalhes sobre a definição de alertas e métricas de visualização.

## <a name="yarn-queue-configuration"></a>Configuração da fila yARN

Hadoop tem vários serviços em toda a sua plataforma distribuída. A YARN (Mais um Negociador de Recursos) coordena estes serviços e aloca recursos de cluster para garantir que qualquer carga seja distribuída uniformemente pelo cluster.

O YARN divide as duas responsabilidades do JobTracker, gestão de recursos e agendamento/monitorização de emprego, em dois daemons: um Gestor global de Recursos, e um ApplicationMaster por aplicação (AM).

O Gestor de Recursos é um *programador puro,* e apenas arbitra os recursos disponíveis entre todas as aplicações concorrentes. O Gestor de Recursos garante que todos os recursos estão sempre em uso, otimizando para várias constantes como SLAs, garantias de capacidade, etc. O ApplicationMaster negoceia recursos do Gestor de Recursos e trabalha com o NodeManager(s) para executar e monitorizar os contentores e o seu consumo de recursos.

Quando vários inquilinos partilham um grande aglomerado, há concorrência para os recursos do cluster. O CapacityScheduler é um programador pluggable que ajuda na partilha de recursos através da fila de pedidos. O CapacityScheduler também apoia *filas hierárquicas* para garantir que os recursos são partilhados entre as subfilas de uma organização, antes que as filas de outras aplicações sejam autorizadas a usar recursos gratuitos.

O YARN permite-nos alocar recursos a estas filas, e mostra-lhe se todos os seus recursos disponíveis estão atribuídos. Para ver informações sobre as suas filas, inscreva-se no Ambari Web UI e, em seguida, selecione **YARN Queue Manager** a partir do menu superior.

![Apache Ambari YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

A página do Gestor de Fila sarn mostra uma lista das suas filas à esquerda, juntamente com a percentagem de capacidade atribuída a cada uma.

![Página de detalhes do Gestor de Fila yARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Para uma análise mais detalhada das suas filas, a partir do painel ambari, selecione o serviço **YARN** da lista à esquerda. Em seguida, sob o menu de dropdown **Quick Links,** selecione **ReI do Gestor de Recursos** por baixo do seu nó ativo.

![Links de menu UI do Gestor de Recursos](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

No UI do Gestor de Recursos, selecione **Scheduler** do menu à esquerda. Vê uma lista das suas filas por baixo das filas de *aplicação.* Aqui pode ver a capacidade utilizada para cada uma das suas filas, quão bem os postos de trabalho são distribuídos entre eles e se quaisquer postos de trabalho estão limitados aos recursos.

![Menu UI do Gestor de Recursos Apache HAdoop](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Estrangulamento de armazenamento

O estrangulamento de desempenho de um cluster pode acontecer ao nível do armazenamento. Este tipo de estrangulamento deve-se, na maioria das vezes, *ao bloqueio* das operações de entrada/saída (IO), que acontecem quando as suas tarefas de execução enviam mais IO do que o serviço de armazenamento pode suportar. Este bloqueio cria uma fila de pedidos de IO à espera de serem processados até que os IOs atuais sejam processados. Os blocos devem-se à *aceleração*do armazenamento , que não é um limite físico, mas sim um limite imposto pelo serviço de armazenamento por um acordo de nível de serviço (SLA). Este limite garante que nenhum cliente ou inquilino pode monopolizar o serviço. O SLA limita o número de IOs por segundo (IOPS) para o Armazenamento De Azure - para mais detalhes, consulte a [Escalabilidade e os objetivos](../storage/common/scalability-targets-standard-account.md)de desempenho para contas de armazenamento padrão .

Se estiver a utilizar o Armazenamento Azure, para obter informações sobre a monitorização de questões relacionadas com o armazenamento, incluindo estrangulamento, ver [Monitor, diagnosticar e resolver problemas](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)o Armazenamento Microsoft Azure .

Se a loja de apoio do seu cluster for O Armazenamento do Lago De dados Azure (ADLS), a sua aceleração é provavelmente devido aos limites de largura de banda. A aceleração neste caso poderia ser identificada observando erros de estrangulamento nos registos de tarefas. Para a ADLS, consulte a secção de estrangulamento para o serviço adequado nestes artigos:

* [Orientação de afinação de desempenho para Apache Hive no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Orientação de afinação de desempenho para MapReduce no Armazenamento de Lago HDInsight e Azure Data](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Orientação de afinação de desempenho para Apache Storm no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Desempenho lento do nó de resolução de problemas

Em alguns casos, a lentidão pode ocorrer devido ao baixo espaço do disco no cluster. Investigue com estas etapas:

1. Utilize o [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar a cada um dos nós.

1. Verifique a utilização do disco executando um dos seguintes comandos:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Reveja a saída e verifique se existem `mnt` ficheiros grandes na pasta ou noutras pastas. Tipicamente, `usercache`as `appcache` pastas , e (mnt/resource/hadoop/fio/local/usercache/hive/appcache/) contêm ficheiros grandes.

1. Se houver ficheiros grandes, ou um trabalho atual está a causar o crescimento do ficheiro ou um trabalho anterior falhado pode ter contribuído para esta questão. Para verificar se este comportamento é causado por um trabalho atual, executar o seguinte comando:

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Se este comando indicar um trabalho específico, pode optar por terminar o trabalho utilizando um comando que se assemelha ao seguinte:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Substitua-a `application_id` com o ID da aplicação. Se não forem indicados empregos específicos, vá para o próximo passo.

1. Depois de o comando acima estar concluído, ou se não forem indicados trabalhos específicos, elimine os ficheiros grandes que identificou com um comando que se assemelhe ao seguinte:

    ```bash
    rm -rf filecache usercache
    ```

Para mais informações sobre questões espaciais de disco, consulte [out of disk space](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Se tem ficheiros grandes que pretende manter mas que estão a contribuir para a questão do espaço de disco baixo, tem de aumentar o seu cluster HDInsight e reiniciar os seus serviços. Depois de completar este procedimento e esperar alguns minutos, irá notar que o armazenamento é libertado e o desempenho habitual do nó é restaurado.

## <a name="next-steps"></a>Passos seguintes

Visite os seguintes links para obter mais informações sobre resolução de problemas e monitorização dos seus clusters:

* [Analisar registos do HDInsight](hdinsight-debug-jobs.md)
* [Aplicativos de depuração com registos YARN Apache Hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Ativar depósitos de pilhas para serviços Apache Hadoop em HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
