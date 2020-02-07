---
title: Dimensionar tamanhos de cluster-Azure HDInsight
description: Dimensionar um cluster Apache Hadoop de forma elástica para corresponder à sua carga de trabalho no Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 035f819cfaad82373f7cb55a7bb2d14fc53bb49b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064636"
---
# <a name="scale-azure-hdinsight-clusters"></a>Dimensionar clusters do Azure HDInsight

O HDInsight fornece elasticidade fornecendo a opção de escalar verticalmente e reduzir o número de nós de trabalho em seus clusters. Essa elasticidade permite que você reduza um cluster após horas ou em fins de semana e expanda-o durante as demandas de negócios de pico.

Se você tiver um processamento de lote periódico, o cluster HDInsight poderá ser escalado verticalmente alguns minutos antes dessa operação, de modo que o cluster tenha uma capacidade de CPU e memória adequada.  Posteriormente, depois que o processamento for concluído e o uso falhar novamente, você poderá reduzir o cluster HDInsight para menos nós de trabalho.

Pode escalar um cluster manualmente utilizando um dos métodos descritos abaixo, ou utilizar opções de [escala automática](hdinsight-autoscale-clusters.md) para ter o sistema automaticamente escalando para cima e para baixo em resposta a CPU, memória e outras métricas.

> [!NOTE]  
> Há suporte apenas para clusters com o HDInsight versão 3.1.3 ou superior. Se não tiver certeza da versão do cluster, você poderá verificar a página Propriedades.

## <a name="utilities-to-scale-clusters"></a>Utilitários para dimensionar clusters

A Microsoft fornece os seguintes utilitários para dimensionar clusters:

|Utilidade | Descrição|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight redimension --grupo](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) de recursos \<Resource group> --name \<Cluster Name> --target-instance-count \<NewSize>|
|[CLI do Azure](hdinsight-administer-use-command-line.md)|cluster azure hdinsight redimensionar \<clusterName> \<Target Instance Count> |
|[Portal do Azure](https://portal.azure.com)|Abra o painel de cluster HDInsight, selecione **o tamanho do Cluster** no menu à esquerda e, em seguida, no painel do tamanho do Cluster, digite o número de nós do trabalhador e selecione Guardar.|  

![Opção portal do Azure dimensionar cluster](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Usando qualquer um desses métodos, você pode dimensionar seu cluster HDInsight para cima ou para baixo em minutos.

> [!IMPORTANT]  
> * A CLI clássica do Azure foi preterida e só deve ser usada com o modelo de implantação clássico. Para todas as outras implementações, utilize o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * O módulo AzureRM do PowerShell foi preterido.  Por favor, utilize o [módulo Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) sempre que possível.

## <a name="impact-of-scaling-operations"></a>Impacto das operações de dimensionamento

Quando **adicionar** nós ao seu cluster HDInsight em execução (escala para cima), quaisquer trabalhos pendentes ou em execução não serão afetados. Novos trabalhos podem ser enviados com segurança enquanto o processo de dimensionamento está em execução. Se a operação de dimensionamento falhar por algum motivo, a falha será tratada para deixar o cluster em um estado funcional.

Se **remover** os nódosos (escala para baixo), quaisquer postos de trabalho pendentes ou em funcionamento falharão quando a operação de escalação estiver concluída. Essa falha ocorre devido a alguns dos serviços reiniciando durante o processo de dimensionamento. Também há um risco de que o cluster fique preso no modo de segurança durante uma operação de dimensionamento manual.

O impacto da alteração do número de nós de dados varia para cada tipo de cluster com suporte do HDInsight:

* Apache Hadoop

    Você pode aumentar diretamente o número de nós de trabalho em um cluster Hadoop em execução sem afetar nenhum trabalho pendente ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. As falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre fique em um estado funcional.

    Quando um cluster Hadoop é reduzido reduzindo o número de nós de dados, alguns dos serviços no cluster são reiniciados. Esse comportamento faz com que todos os trabalhos em execução e pendentes falhem na conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.

* Apache HBase

    Você pode adicionar ou remover nós diretamente em seu cluster HBase enquanto ele está em execução. Os servidores regionais são balanceados automaticamente em alguns minutos após a conclusão da operação de dimensionamento. No entanto, você também pode balancear manualmente os servidores regionais fazendo logon no cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Para obter mais informações sobre a utilização da concha HBase, consulte [Iniciar-se com um exemplo Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Você pode adicionar ou remover nós de dados diretamente em seu cluster Storm enquanto ele estiver em execução. No entanto, após uma conclusão bem-sucedida da operação de dimensionamento, será necessário reequilibrar a topologia.

    O rebalanceamento pode ser realizado de duas maneiras:

  * Interface do usuário da Web do Storm
  * Ferramenta de interface de linha de comando (CLI)

    Consulte a [documentação](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) da Tempestade Apache para mais detalhes.

    A interface do usuário da Web do Storm está disponível no cluster HDInsight:

    ![Rebalanceamento de escala do HDInsight Storm](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Aqui está um exemplo de comando da CLI para reequilibrar a topologia do Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Como reduzir com segurança um cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Reduzir verticalmente um cluster com trabalhos em execução

Para evitar que seus trabalhos em execução falhem durante uma operação de redução, você pode experimentar três coisas:

1. Aguarde até que os trabalhos sejam concluídos antes de reduzir horizontalmente o cluster.
1. Encerre manualmente os trabalhos.
1. Reenvie os trabalhos após a operação de dimensionamento ser concluída.

Para ver uma lista de trabalhos pendentes e em execução, pode utilizar o **YARN Resource Manager UI,** seguindo estes passos:

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster.  Consulte [a Lista e mostre](./hdinsight-administer-use-portal-linux.md#showClusters) os agrupamentos para obter as instruções. O cluster é aberto em uma nova página do Portal.
2. Da vista principal, navegue até **aos dashboards do Cluster** > casa **ambari**. Insira suas credenciais de cluster.
3. A partir do Ambari UI, selecione **YARN** na lista de serviços no menu à esquerda.  
4. A partir da página YARN, selecione **Quick Links** e paire sobre o nó de cabeça ativo e, em seguida, selecione **ResourceManager UI**.

    ![IU do ResourceManager de links rápidos do Apache Ambari](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Pode aceder diretamente ao UI do ResourceManager com `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Você verá uma lista de trabalhos, juntamente com seu estado atual. Na captura de tela, há um trabalho em execução no momento:

![Aplicativos de interface do usuário do ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Para eliminar manualmente esse aplicativo em execução, execute o seguinte comando no Shell SSH:

```bash
yarn application -kill <application_id>
```

Por exemplo:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Travando no modo de segurança

Quando você reduz verticalmente um cluster, o HDInsight usa as interfaces de gerenciamento do Apache Ambari para primeiro desativar os nós de trabalho adicionais, que replicam seus blocos HDFS para outros nós de trabalho online. Depois disso, o HDInsight dimensiona com segurança o cluster para baixo. O HDFS entra em modo de segurança durante a operação de dimensionamento e deve sair quando o dimensionamento for concluído. Em alguns casos, no entanto, o HDFS fica preso no modo de segurança durante uma operação de dimensionamento devido à replicação do bloco de arquivos.

Por predefinição, o HDFS é configurado com uma definição `dfs.replication` de 1, que controla quantas cópias de cada bloco de ficheiros estão disponíveis. Cada cópia de um bloco de arquivos é armazenada em um nó diferente do cluster.

Quando o HDFS detecta que o número esperado de cópias de bloco não está disponível, o HDFS entra no modo de segurança e Ambari gera alertas. Se o HDFS entrar no modo de segurança para uma operação de dimensionamento, mas não puder sair do modo de segurança porque o número necessário de nós não for detectado para replicação, o cluster poderá ficar preso no modo de segurança.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo quando o modo de segurança está ativado

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Pode rever os registos de nó do nome da pasta `/var/log/hadoop/hdfs/`, perto da altura em que o cluster foi dimensionado, para ver quando entrou no modo de segurança. Os ficheiros de registo são chamados `Hadoop-hdfs-namenode-<active-headnode-name>.*`.

A causa raiz dos erros anteriores é que o hive depende de arquivos temporários no HDFS durante a execução de consultas. Quando o HDFS entra no modo de segurança, o hive não pode executar consultas porque ele não pode gravar no HDFS. Os arquivos temporários no HDFS estão localizados na unidade local montada nas VMs de nó de trabalho individuais e replicados entre outros nós de trabalho em três réplicas, no mínimo.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Como impedir que o HDInsight fique preso no modo de segurança

Há várias maneiras de impedir que o HDInsight seja deixado no modo de segurança:

* Interrompa todos os trabalhos do hive antes de reduzir o HDInsight. Como alternativa, agende o processo de redução para baixo para evitar conflitos com a execução de trabalhos do hive.
* Limpe manualmente o risco da Hive `tmp` ficheiros de diretório no HDFS antes de reduzir.
* Reduza verticalmente o HDInsight para três nós de trabalho, no mínimo. Evite ficar tão baixo quanto um nó de trabalho.
* Execute o comando para sair do modo de segurança, se necessário.

As seções a seguir descrevem essas opções.

#### <a name="stop-all-hive-jobs"></a>Parar todos os trabalhos do hive

Interrompa todos os trabalhos do hive antes de reduzir horizontalmente para um nó de trabalho. Se sua carga de trabalho estiver agendada, execute a escala vertical após a conclusão da tarefa do hive.

Parar os trabalhos do hive antes do dimensionamento, ajuda a minimizar o número de arquivos transitórios na pasta tmp (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpar manualmente os arquivos temporários do hive

Se o hive for deixado para trás dos arquivos temporários, você poderá limpar manualmente esses arquivos antes de reduzir verticalmente para evitar o modo de segurança.

1. Verifique qual a localização que está a ser usada para ficheiros temporários da Hive, olhando para a propriedade de configuração `hive.exec.scratchdir`. Este parâmetro está definido dentro `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Pare os serviços do hive e certifique-se de que todas as consultas e trabalhos estejam concluídos.
2. Enumerar o conteúdo do diretório de riscos acima encontrado, `hdfs://mycluster/tmp/hive/` para ver se contém ficheiros:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Aqui está um exemplo de saída quando existem arquivos:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Se você souber que o hive é feito com esses arquivos, poderá removê-los. Certifique-se de que o hive não tenha nenhuma consulta em execução examinando a página da interface do usuário do yarn ResourceManager.

    Exemplo de linha de comando para remover arquivos do HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Dimensionar o HDInsight para três ou mais nós de trabalho

Se os clusters ficarem presos no modo de segurança com frequência ao reduzir verticalmente para menos de três nós de trabalho e as etapas anteriores não funcionarem, você poderá evitar que o cluster vá para o modo de segurança completamente mantendo pelo menos três nós de trabalho.

Reter três nós de trabalho é mais dispendioso do que escalar verticalmente para apenas um nó de trabalho, mas impedirá que o cluster fique preso no modo de segurança.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Scale HDInsight até um nó de trabalhador

Mesmo quando o aglomerado é reduzido a 1 nó, o nó de trabalhador0 0 ainda sobreviverá. O nó 0 dos trabalhadores nunca pode ser desativado.

#### <a name="run-the-command-to-leave-safe-mode"></a>Execute o comando para sair do modo de segurança

A opção final é executar o comando sair do modo de segurança. Se você souber que o motivo para o HDFS entrar no modo de segurança é devido ao arquivo de Hive em replicação, você poderá executar o seguinte comando para sair do modo de segurança:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Reduzir verticalmente um cluster do Apache HBase

Os servidores de região são balanceados automaticamente em alguns minutos após a conclusão de uma operação de dimensionamento. Para balancear manualmente os servidores de região, conclua as seguintes etapas:

1. Conecte-se ao cluster HDInsight usando SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Inicie o Shell do HBase:

    ```bash
    hbase shell
    ```

3. Use o seguinte comando para balancear manualmente os servidores de região:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Passos seguintes

* [Escala automaticamente os clusters Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
