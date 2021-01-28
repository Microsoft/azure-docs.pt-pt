---
title: Tamanhos de cluster de escala - Azure HDInsight
description: Dimensione um aglomerado Apache Hadoop elasticamente para combinar com a sua carga de trabalho em Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 3524b5d2274c52aa94fa1c3420fb0d3245d9b730
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932063"
---
# <a name="scale-azure-hdinsight-clusters"></a>Clusters Azure HDInsight

O HDInsight proporciona elasticidade com opções para escalar e reduzir o número de nós de trabalhadores nos seus clusters. Esta elasticidade permite encolher um aglomerado fora de horas ou aos fins de semana. E expandi-lo durante as exigências máximas dos negócios.

Dimensione o seu cluster antes do processamento periódico do lote para que o cluster tenha recursos adequados.  Após o processamento completo, e a utilização desce, reduza o cluster HDInsight para menos nós de trabalhadores.

Pode escalar um cluster manualmente utilizando um dos métodos descritos abaixo. Também pode utilizar opções [de autoescala](hdinsight-autoscale-clusters.md) para escalar automaticamente para cima e para baixo em resposta a determinadas métricas.

> [!NOTE]  
> Apenas os clusters com a versão HDInsight 3.1.3 ou superior são suportados. Se não tiver a certeza da versão do seu cluster, pode consultar a página Propriedades.

## <a name="utilities-to-scale-clusters"></a>Utilitários para escalar clusters

A Microsoft fornece os seguintes utilitários para dimensionar os clusters:

|Utilitário | Descrição|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[CLI do Azure](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[CLI Clássica do Azure](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Portal do Azure](https://portal.azure.com)|Abra o painel de agrupamento HDInsight, selecione o tamanho do **Cluster** no menu da esquerda, depois na vidraça do tamanho do Cluster, escreva o número de nós de trabalhador e selecione Save.|  

![Opção de cluster de escala de portal Azure](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Utilizando qualquer um destes métodos, pode escalar o seu cluster HDInsight para cima ou para baixo em minutos.

> [!IMPORTANT]  
> * O CLI clássico do Azure é prectado e só deve ser usado com o modelo de implementação clássico. Para todas as outras missões, utilize o [CLI Azure](/cli/azure/).
> * O módulo PowerShell AzureRM está precotado.  Por favor, utilize o [módulo Az](/powershell/azure/new-azureps-module-az) sempre que possível.

## <a name="impact-of-scaling-operations"></a>Impacto das operações de escala

Quando **adicionar** nós ao seu cluster HDInsight em execução (escala acima), os empregos não serão afetados. Novos postos de trabalho podem ser submetidos com segurança enquanto o processo de escala está em curso. Se a operação de escala falhar, a falha deixará o seu aglomerado num estado funcional.

Se **remover** os nós (escala para baixo), os trabalhos pendentes ou em funcionamento falharão quando a operação de escala estiver concluída. Esta falha deve-se a alguns dos serviços que reiniciaram durante o processo de dimensionamento. O seu cluster pode ficar preso em modo de segurança durante uma operação de escala manual.

O impacto da alteração do número de nós de dados varia para cada tipo de cluster suportado por HDInsight:

* Apache Hadoop

    Você pode aumentar perfeitamente o número de nós de trabalhadores em um cluster Hadoop em execução sem afetar qualquer emprego. Novos postos de trabalho também podem ser apresentados enquanto a operação está em curso. Falhas numa operação de escala são tratadas graciosamente. O aglomerado é sempre deixado num estado funcional.

    Quando um cluster Hadoop é reduzido com menos nós de dados, alguns serviços são reiniciados. Este comportamento faz com que todos os trabalhos de funcionamento e pendentes falhem na conclusão da operação de escala. No entanto, pode reenviar os trabalhos assim que a operação estiver concluída.

* Apache HBase

    Pode adicionar ou remover os nós perfeitamente no seu cluster HBase enquanto está em funcionamento. Os Servidores Regionais são automaticamente equilibrados dentro de poucos minutos após completarem a operação de escala. No entanto, pode equilibrar manualmente os servidores regionais. Inicie sessão no headnode do cluster e execute os seguintes comandos:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Para obter mais informações sobre a utilização da concha HBase, consulte [Começar com um exemplo Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Pode adicionar ou remover os nós de dados sem problemas enquanto a Tempestade está em funcionamento. No entanto, depois de uma conclusão bem sucedida da operação de escala, terá de reequilibrar a topologia. O reequilíbrio permite que a topologia reajuste [as definições de paralelismo](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) com base no novo número de nós no cluster. Para reequilibrar as topologias em execução, utilize uma das seguintes opções:

  * UI da teia de tempestade

    Utilize os seguintes passos para reequilibrar uma topologia utilizando a UI da tempestade.

    1. Abra `https://CLUSTERNAME.azurehdinsight.net/stormui` no seu navegador web, onde está o nome do seu cluster `CLUSTERNAME` Storm. Se solicitado, insira o nome e palavra-passe do administrador de cluster HDInsight (administrador) especificado ao criar o cluster.

    1. Selecione a topologia que pretende reequilibrar e, em seguida, selecione o botão **Reequilibrar.** Introduza o atraso antes de terminar a operação de reequilíbrio.

        ![Reequilibrar escala de tempestade HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

  * Ferramenta de interface de linha de comando (CLI)

    Ligue ao servidor e use o seguinte comando para reequilibrar uma topologia:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Também pode especificar parâmetros para anular as dicas de paralelismo originalmente fornecidas pela topologia. Por exemplo, o código abaixo reconfigura a `mytopology` topologia para 5 processos de trabalhadores, 3 executores para o componente de bico azul e 10 executores para o componente de parafuso amarelo.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Deve reequilibrar réplicas de partição após operações de escalonamento. Para mais informações, consulte a [alta disponibilidade de dados com Apache Kafka no documento HDInsight.](./kafka/apache-kafka-high-availability.md)

* Colmeia Apache LLAP

    Após a escala para `N` os nós dos trabalhadores, o HDInsight definirá automaticamente as seguintes configurações e reiniciará a Colmeia.

  * Máxima total de consultas simultâneas: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Número de nós usados pelo LLAP da Hive: `num_llap_nodes  = N`
  * Número de nó(s) para a execução do daemon Hive LLAP: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Como escalar com segurança um cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Reduza um aglomerado com empregos em execução

Para evitar que os seus trabalhos de corrida falhem durante uma operação de escala, pode tentar três coisas:

1. Espere que os trabalhos terminem antes de escalonar o seu aglomerado.
1. Terminar manualmente os trabalhos.
1. Reenviar os postos de trabalho após a conclusão da operação de escalonamento.

Para ver uma lista de empregos pendentes e em execução, pode utilizar o **UI do Gestor de Recursos** yarn, seguindo estes passos:

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster.  O cluster é aberto numa nova página do portal.
2. A partir da vista principal, navegue até cluster **dashboards**  >  **Ambari casa.** Insira as suas credenciais de agrupamento.
3. A partir da UI Ambari, selecione **YARN** na lista de serviços no menu à esquerda.  
4. Na página YARN, selecione **Links Rápidos** e paire sobre o nó de cabeça ativo e, em seguida, selecione **Resource Manager UI**.

    ![Apache Ambari liga rapidamente Resource Manager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Pode aceder diretamente à UI do Gestor de Recursos com `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

Vê-se uma lista de empregos, juntamente com o estado atual. Na imagem, há um trabalho em execução:

![Aplicações UI do Gestor de Recursos](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Para eliminar manualmente a aplicação de execução, execute o seguinte comando a partir da concha SSH:

```bash
yarn application -kill <application_id>
```

Por exemplo:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Bloqueado no modo seguro

Quando se reduz um cluster, o HDInsight utiliza interfaces de gestão Apache Ambari para primeiro desativar os nós de trabalhador extra. Os nós replicam os seus blocos HDFS a outros nós de trabalhadores online. Depois disso, o HDInsight escala o cluster com segurança para baixo. O HDFS entra em modo de segurança durante a operação de escala. O HDFS deve sair assim que o escalonamento estiver terminado. Em alguns casos, no entanto, o HDFS fica preso em modo de segurança durante uma operação de escala devido à sub-replicação do bloco de ficheiros.

Por predefinição, o HDFS é configurado com uma `dfs.replication` definição de 1, que controla quantas cópias de cada bloco de ficheiros estão disponíveis. Cada cópia de um bloco de ficheiros é armazenada num nó diferente do cluster.

Quando o número esperado de cópias de blocos não está disponível, o HDFS entra no modo de segurança e a Ambari gera alertas. O HDFS pode entrar no modo de segurança para uma operação de escala. O cluster pode ficar preso em modo de segurança se o número necessário de nós não for detetado para replicação.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo quando o modo de segurança é ligado

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Pode rever os registos de nó de nome da `/var/log/hadoop/hdfs/` pasta, perto da altura em que o cluster foi dimensionado, para ver quando entrou no modo de segurança. Os ficheiros de registo são `Hadoop-hdfs-namenode-<active-headnode-name>.*` nomeados.

A causa principal foi que a Hive depende de ficheiros temporários em HDFS enquanto executa consultas. Quando o HDFS entra no modo de segurança, a Hive não pode executar consultas porque não pode escrever para o HDFS. Os ficheiros temporários em HDFS estão localizados na unidade local montada nos VMs de nó de trabalhador individual. Os ficheiros são replicados entre outros nós operários em três réplicas, no mínimo.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Como evitar que o HDInsight se encrava no modo de segurança

Existem várias formas de evitar que o HDInsight seja deixado em modo de segurança:

* Pare todos os trabalhos de Colmeia antes de reduzir o HDInsight. Alternadamente, marque o processo de escala para evitar conflitos com a gestão de trabalhos de Colmeia.
* Limpe manualmente os ficheiros de raspadinhas da Hive `tmp` em HDFS antes de escalonar.
* Apenas reduza o HDInsight para três nós operários, no mínimo. Evite descer tão baixo como um nó de trabalhador.
* Executar o comando para deixar o modo de segurança, se necessário.

As seguintes secções descrevem estas opções.

#### <a name="stop-all-hive-jobs"></a>Parem todos os trabalhos da Colmeia

Parem todos os trabalhos de colmeia antes de se escalonarem para um nó de trabalhador. Se a sua carga de trabalho estiver programada, execute a sua escala depois do trabalho da Colmeia.

Parar os trabalhos de Colmeia antes de escalonar, ajuda a minimizar o número de ficheiros de risco na pasta tmp (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpe manualmente os ficheiros de risco da Hive

Se a Hive tiver deixado para trás ficheiros temporários, então pode limpar manualmente esses ficheiros antes de reduzir a escala para evitar o modo de segurança.

1. Verifique qual a localização que está a ser usada para ficheiros temporários da Hive, olhando para a `hive.exec.scratchdir` propriedade de configuração. Este parâmetro é definido dentro `/etc/hive/conf/hive-site.xml` de :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Pare os serviços da Hive e certifique-se de que todas as consultas e trabalhos estão concluídos.

1. Listar o conteúdo do diretório de risco encontrado acima, `hdfs://mycluster/tmp/hive/` para ver se contém algum ficheiro:

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

1. Se souber que a Hive acabou com estes ficheiros, pode removê-los. Certifique-se de que a Hive não tem nenhuma consulta a decorrer olhando para a página de UI do Gestor de Recursos de Yarn.

    Linha de comando exemplo para remover ficheiros do HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Escala HDInsight para três ou mais nómada de trabalhadores

Se os seus aglomerados ficarem presos frequentemente em modo de segurança ao reduzir para menos de três nós de trabalhadores, então mantenha pelo menos três nós de trabalhadores.

Ter três nós operários é mais caro do que reduzir para apenas um nó de trabalhador. No entanto, esta ação evitará que o seu cluster fique preso em modo de segurança.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Escala HDInsight até um nó de trabalhador

Mesmo quando o aglomerado é reduzido a um nó, o nó 0 do trabalhador ainda sobreviverá. O nó 0 do trabalhador nunca poderá ser desativado.

#### <a name="run-the-command-to-leave-safe-mode"></a>Executar o comando para deixar o modo de segurança

A opção final é executar o comando do modo de segurança de licença. Se o HDFS entrou no modo de segurança devido à sub-replicação do ficheiro Hive, execute o seguinte comando para deixar o modo de segurança:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Reduza um aglomerado Apache HBase

Os servidores da região são automaticamente equilibrados dentro de poucos minutos após completar uma operação de escala. Para equilibrar manualmente os servidores da região, complete os seguintes passos:

1. Ligue-se ao cluster HDInsight utilizando SSH. Para obter mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Inicie a concha HBase:

    ```bash
    hbase shell
    ```

3. Utilize o seguinte comando para equilibrar manualmente os servidores da região:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Próximos passos

* [Dimensionar automaticamente os clusters do Azure HDInsight](hdinsight-autoscale-clusters.md)

Para obter informações específicas sobre o dimensionamento do seu cluster HDInsight, consulte:

* [Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerir os clusters Apache Hadoop em HDInsight utilizando o Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)