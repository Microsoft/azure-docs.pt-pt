---
title: Tamanhos de cluster de escala - Azure HDInsight
description: Dimensione um cluster Apache Hadoop elástico para combinar com a sua carga de trabalho em Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 2dae0f662eefa7f7b1f56d057cd47f1cb92244ce
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592065"
---
# <a name="scale-azure-hdinsight-clusters"></a>Clusters Scale Azure HDInsight

O HDInsight fornece elasticidade com opções para escalar e reduzir o número de nós dos trabalhadores nos seus clusters. Esta elasticidade permite-lhe encolher um aglomerado fora de horas ou aos fins de semana. E expandi-lo durante as exigências de negócios.

Aumentar o seu cluster antes do processamento periódico do lote para que o cluster tenha recursos adequados. Após o processamento completo, e o uso desce, reduza o cluster HDInsight para menos nós de trabalhador.

Pode escalar um cluster manualmente utilizando um dos métodos descritos abaixo. Também pode utilizar opções de [escala automática](hdinsight-autoscale-clusters.md) para escalar automaticamente para cima e para baixo em resposta a determinadas métricas.

> [!NOTE]  
> Apenas são suportados clusters com a versão 3.1.3 ou superior do HDInsight. Se não tiver a certeza da versão do seu cluster, pode consultar a página Propriedades.

## <a name="utilities-to-scale-clusters"></a>Utilitários para escalar clusters

A Microsoft fornece os seguintes utilitários para escalar clusters:

|Utility | Descrição|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[`Set-AzHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) | [`az hdinsight resize`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[CLI Clássica do Azure](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Portal do Azure](https://portal.azure.com)|Abra o painel de cluster HDInsight, selecione **o tamanho do Cluster** no menu à esquerda e, em seguida, no painel do tamanho do Cluster, digite o número de nós do trabalhador e selecione Guardar.|  

![Opção de cluster de escala de portal Azure](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Utilizando qualquer um destes métodos, pode escalar o seu cluster HDInsight para cima ou para baixo em minutos.

> [!IMPORTANT]  
> * O CLI clássico azure é depreciado e só deve ser usado com o modelo de implantação clássico. Para todas as outras implementações, utilize o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
> * O módulo PowerShell AzureRM é depreciado.  Por favor, utilize o [módulo Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) sempre que possível.

## <a name="impact-of-scaling-operations"></a>Impacto das operações de escala

Quando **adicionar** nós ao seu cluster HDInsight (escala ção), os empregos não serão afetados. Novos postos de trabalho podem ser submetidos com segurança enquanto o processo de escala está em curso. Se a operação de escala falhar, a falha deixará o seu cluster num estado funcional.

Se **retirar** os nódosos (escala para baixo), os postos de trabalho pendentes ou em funcionamento falharão quando a operação de escala ção estiver concluída. Esta falha deve-se ao reinício de alguns dos serviços durante o processo de escala. O seu cluster pode ficar preso em modo de segurança durante uma operação de escala manual.

O impacto da alteração do número de nós de dados varia para cada tipo de cluster suportado pelo HDInsight:

* Apache Hadoop

    Pode aumentar perfeitamente o número de nós dos trabalhadores num aglomerado de Hadoop em execução sem ter impacto em quaisquer empregos. Novos postos de trabalho também podem ser submetidos enquanto a operação estiver em curso. Falhas numa operação de escala são graciosamente tratadas. O aglomerado é sempre deixado num estado funcional.

    Quando um cluster Hadoop é reduzido com menos nódeos de dados, alguns serviços são reiniciados. Este comportamento faz com que todos os postos de trabalho pendentes falhem no final da operação de escala. No entanto, pode reenviar os trabalhos assim que a operação estiver concluída.

* Apache HBase

    Pode adicionar ou remover os nós do seu cluster HBase enquanto estiver em funcionamento. Os Servidores Regionais são automaticamente equilibrados dentro de poucos minutos após o completar a operação de escala. No entanto, pode equilibrar manualmente os servidores regionais. Inicie sessão no headnode do cluster e execute os seguintes comandos:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Para obter mais informações sobre a utilização da concha HBase, consulte [Iniciar-se com um exemplo Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Pode adicionar ou remover os nódosos de dados enquanto a Tempestade está em execução. No entanto, após uma conclusão bem sucedida da operação de escala, terá de reequilibrar a topologia. O reequilíbrio permite que a topologia reajuste as definições de [paralelismo](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) com base no novo número de nós no cluster. Para reequilibrar as topoologias de execução, utilize uma das seguintes opções:

  * UI web tempestade

    Utilize os seguintes passos para reequilibrar uma topologia utilizando a Tempestade UI.

    1. Abra `https://CLUSTERNAME.azurehdinsight.net/stormui` no seu navegador `CLUSTERNAME` web, onde está o nome do seu cluster Storm. Se solicitado, introduza o nome e a palavra-passe do administrador do cluster HDInsight (administrador) especificado ao criar o cluster.

    1. Selecione a topologia que deseja reequilibrar e, em seguida, selecione o botão **Reequilibrar.** Introduza o atraso antes de a operação de reequilíbrio estar pronta.

        ![Reequilíbrio da escala de tempestade HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

  * Ferramenta de interface de linha de comando (CLI)

    Ligue-se ao servidor e utilize o seguinte comando para reequilibrar uma topologia:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Também pode especificar parâmetros para anular as dicas de paralelismo originalmente fornecidas pela topologia. Por exemplo, o código `mytopology` abaixo reconfigura a topologia a 5 processos de trabalhadores, 3 executores para o componente de bico azul e 10 executores para o componente de parafusoamarelo.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Deve reequilibrar as réplicas da partição após operações de escala. Para mais informações, consulte a alta disponibilidade de dados com Apache Kafka no documento [HDInsight.](./kafka/apache-kafka-high-availability.md)

## <a name="how-to-safely-scale-down-a-cluster"></a>Como escalar com segurança um aglomerado

### <a name="scale-down-a-cluster-with-running-jobs"></a>Escalde um cluster com empregos em execução

Para evitar que os seus trabalhos de corrida falhem durante uma operação de redução de escala, pode tentar três coisas:

1. Espere que os trabalhos completem antes de reduzir o seu aglomerado.
1. Termine manualmente os trabalhos.
1. Reenvie os postos de trabalho após a conclusão da operação de escala.

Para ver uma lista de trabalhos pendentes e em execução, pode utilizar o **YARN Resource Manager UI,** seguindo estes passos:

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster.  O cluster é aberto numa nova página do portal.
2. Da vista principal, navegue até aos **dashboards** > **ambari.** Insira as credenciais do seu cluster.
3. A partir do Ambari UI, selecione **YARN** na lista de serviços no menu à esquerda.  
4. A partir da página YARN, selecione **Quick Links** e paire sobre o nó de cabeça ativo e, em seguida, selecione Resource **Manager UI**.

    ![Apache Ambari liga rapidamente o Gestor de Recursos UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Pode aceder diretamente ao UI `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`do Gestor de Recursos com .

Vê-se uma lista de empregos, juntamente com o seu estado atual. Na imagem, há um trabalho em andamento:

![Aplicações ui gestorde recursos](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Para matar manualmente a aplicação de execução, execute o seguinte comando a partir da concha SSH:

```bash
yarn application -kill <application_id>
```

Por exemplo:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Ficar preso em modo de segurança

Quando reduz um cluster, o HDInsight utiliza interfaces de gestão Apache Ambari para primeiro desativar os nós de trabalhadores extra. Os nódosos replicam os seus blocos HDFS a outros nódos os trabalhadores online. Depois disso, o HDInsight escala com segurança o cluster para baixo. O HDFS entra em modo de segurança durante a operação de escala. HDFS deve sair quando a escala estiver terminada. Em alguns casos, no entanto, o HDFS fica preso em modo de segurança durante uma operação de escala devido à sub-replicação do bloco de ficheiros.

Por predefinição, o HDFS é configurado com uma `dfs.replication` definição de 1, que controla quantas cópias de cada bloco de ficheiros estão disponíveis. Cada cópia de um bloco de ficheiros é armazenada num nó diferente do cluster.

Quando o número esperado de cópias de blocos não está disponível, o HDFS entra no modo de segurança e ambari gera alertas. O HDFS pode entrar em modo de segurança para uma operação de escala. O cluster pode ficar preso em modo de segurança se o número necessário de nós não for detetado para replicação.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo quando o modo de segurança é ligado

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Pode rever os registos de `/var/log/hadoop/hdfs/` nó do nome da pasta, perto da altura em que o cluster foi dimensionado, para ver quando entrou no modo de segurança. Os ficheiros `Hadoop-hdfs-namenode-<active-headnode-name>.*`de registo são nomeados.

A causa principal foi que a Hive depende de ficheiros temporários no HDFS enquanto executa consultas. Quando o HDFS entra em modo de segurança, a Hive não pode executar consultas porque não consegue escrever para o HDFS. Os ficheiros temporários em HDFS estão localizados na unidade local montada nos VMs do nó de trabalhador estoiro individual. Os ficheiros são replicados entre outros nós operários em três réplicas, no mínimo.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Como evitar que o HDInsight se prenda em modo de segurança

Existem várias formas de evitar que o HDInsight seja deixado em modo de segurança:

* Pare todos os trabalhos da Colmeia antes de reduzir o HDInsight. Alternadamente, agende o processo de redução da escala para evitar conflitos com a gestão de postos de trabalho na Colmeia.
* Limpe manualmente os ficheiros de diretório de risco `tmp` da Hive no HDFS antes de reduzir.
* Apenas reduza o HDInsight para três nós de trabalhador, no mínimo. Evite ir tão baixo como um nó de trabalhador.
* Executar o comando para deixar o modo de segurança, se necessário.

As seguintes secções descrevem estas opções.

#### <a name="stop-all-hive-jobs"></a>Pare todos os trabalhos da Colmeia

Pare todos os trabalhos da Colmeia antes de reduzir para um nó de trabalhador. Se a sua carga de trabalho estiver programada, execute a sua redução de escala após o trabalho da Colmeia.

Parar os trabalhos da Colmeia antes de escalonar, ajuda a minimizar o número de ficheiros de risco na pasta TMP (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpe manualmente os ficheiros de risco da Hive

Se a Hive deixou ficheiros temporários, então pode limpar manualmente esses ficheiros antes de se escalonar para evitar o modo de segurança.

1. Verifique qual a localização que está a ser `hive.exec.scratchdir` usada para ficheiros temporários da Hive, olhando para a propriedade de configuração. Este parâmetro está `/etc/hive/conf/hive-site.xml`definido dentro de:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Pare os serviços da Hive e certifique-se de que todas as consultas e trabalhos estão concluídos.

1. Enumera rine o conteúdo do `hdfs://mycluster/tmp/hive/` diretório de risco acima encontrado, para ver se contém ficheiros:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Aqui está uma saída de amostra quando existem ficheiros:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Se souber que a Hive acabou com estes ficheiros, pode removê-los. Certifique-se de que a Hive não tem nenhuma dúvida a correr olhando na página ui do Gestor de Recursos de Fios.

    Linha de comando de exemplo para remover ficheiros do HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Escala HDInsight para três ou mais nós de trabalhador

Se os seus agrupamentos ficarem presos em modo de segurança frequentemente ao escalonar para menos de três nós de trabalhador, então guarde pelo menos três nós de trabalhador.

Ter três nós operários é mais caro do que reduzir para apenas um nó de trabalhador. No entanto, esta ação evitará que o seu cluster fique preso em modo de segurança.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Scale HDInsight até um nó de trabalhador

Mesmo quando o aglomerado é reduzido a um nó, o nó de trabalhador0 0 ainda sobreviverá. O nó 0 dos trabalhadores nunca pode ser desativado.

#### <a name="run-the-command-to-leave-safe-mode"></a>Executar o comando para deixar o modo de segurança

A opção final é executar o comando do modo de segurança de saída. Se o HDFS entrar no modo de segurança por causa da sub-replicação do ficheiro Hive, execute o seguinte comando para deixar o modo de segurança:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Escalde um cluster Apache HBase

Os servidores da região são automaticamente equilibrados dentro de alguns minutos após completar uma operação de escala. Para equilibrar manualmente os servidores da região, complete os seguintes passos:

1. Ligue-se ao cluster HDInsight utilizando O SSH. Para mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Inicie a concha HBase:

    ```bash
    hbase shell
    ```

3. Utilize o seguinte comando para equilibrar manualmente os servidores da região:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Passos seguintes

* [Escala automaticamente os clusters Azure HDInsight](hdinsight-autoscale-clusters.md)

Para obter informações específicas sobre a escala do seu cluster HDInsight, consulte:

* [Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerir os clusters Apache Hadoop em HDInsight utilizando o Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)
