---
title: Tamanhos de cluster de escala - Azure HDInsight
description: Dimensione um cluster Apache Hadoop elástico para combinar com a sua carga de trabalho em Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: a88682cd2217850e336afc2f9ef5af84c0d8cb82
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389522"
---
# <a name="scale-azure-hdinsight-clusters"></a>Clusters Scale Azure HDInsight

O HDInsight proporciona elasticidade, dando-lhe a opção de escalar e reduzir o número de nós dos trabalhadores nos seus clusters. Esta elasticidade permite-lhe encolher um cluster fora de horas ou aos fins de semana, e expandi-lo durante as exigências de negócios.

Se tiver um processamento periódico de lote, o cluster HDInsight pode ser dimensionado alguns minutos antes dessa operação, de modo que o seu cluster tenha memória adequada e potência cpu.  Mais tarde, após o processamento e o uso descer novamente, pode reduzir o cluster HDInsight para menos nós de trabalhador.

Pode escalar um cluster manualmente utilizando um dos métodos descritos abaixo, ou utilizar opções de [escala automática](hdinsight-autoscale-clusters.md) para ter o sistema automaticamente escalando para cima e para baixo em resposta a CPU, memória e outras métricas.

> [!NOTE]  
> Apenas são suportados clusters com a versão 3.1.3 ou superior do HDInsight. Se não tiver a certeza da versão do seu cluster, pode consultar a página Propriedades.

## <a name="utilities-to-scale-clusters"></a>Utilitários para escalar clusters

A Microsoft fornece os seguintes utilitários para escalar clusters:

|Utilidade | Descrição|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight redimension --grupo](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) de recursos \<Resource group> --name \<Cluster Name> --workernode-count \<NewSize>|
|[ClI clássico azure](hdinsight-administer-use-command-line.md)|cluster azure hdinsight redimensionar \<clusterName> \<Target Instance Count> |
|[Portal do Azure](https://portal.azure.com)|Abra o painel de cluster HDInsight, selecione **o tamanho do Cluster** no menu à esquerda e, em seguida, no painel do tamanho do Cluster, digite o número de nós do trabalhador e selecione Guardar.|  

![Opção de cluster de escala de portal Azure](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Utilizando qualquer um destes métodos, pode escalar o seu cluster HDInsight para cima ou para baixo em minutos.

> [!IMPORTANT]  
> * O CLI clássico azure é depreciado e só deve ser usado com o modelo de implantação clássico. Para todas as outras implementações, utilize o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * O módulo PowerShell AzureRM é depreciado.  Por favor, utilize o [módulo Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) sempre que possível.

## <a name="impact-of-scaling-operations"></a>Impacto das operações de escala

Quando **adicionar** nós ao seu cluster HDInsight em execução (escala para cima), quaisquer trabalhos pendentes ou em execução não serão afetados. Novos postos de trabalho podem ser submetidos com segurança enquanto o processo de escala está em curso. Se a operação de escala falhar por qualquer motivo, a falha será tratada para deixar o seu cluster em estado funcional.

Se **remover** os nódosos (escala para baixo), quaisquer postos de trabalho pendentes ou em funcionamento falharão quando a operação de escalação estiver concluída. Esta falha deve-se ao reinício de alguns dos serviços durante o processo de escala. Existe também o risco de o seu cluster ficar preso em modo de segurança durante uma operação de escala manual.

O impacto da alteração do número de nós de dados varia para cada tipo de cluster suportado pelo HDInsight:

* Apache Hadoop

    Pode aumentar perfeitamente o número de nós dos trabalhadores num aglomerado hadoop que está a funcionar sem ter impacto em quaisquer postos de trabalho pendentes ou em funcionamento. Novos postos de trabalho também podem ser submetidos enquanto a operação estiver em curso. Falhas numa operação de escala são manuseadas graciosamente de modo a que o cluster seja sempre deixado em estado funcional.

    Quando um cluster Hadoop é reduzido reduzindo o número de nós de dados, alguns dos serviços do cluster são reiniciados. Este comportamento faz com que todos os postos de trabalho pendentes falhem no final da operação de escala. No entanto, pode reenviar os trabalhos assim que a operação estiver concluída.

* Apache HBase

    Pode adicionar ou remover os nós do seu cluster HBase enquanto estiver em funcionamento. Os Servidores Regionais são automaticamente equilibrados dentro de poucos minutos após o completar a operação de escala. No entanto, também pode equilibrar manualmente os servidores regionais iniciando sessão no headnode do cluster e executando os seguintes comandos a partir de uma janela de solicitação de comando:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Para obter mais informações sobre a utilização da concha HBase, consulte [Iniciar-se com um exemplo Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Pode adicionar ou remover os nós de dados do seu aglomerado de tempestades enquanto estiver em execução. No entanto, após uma conclusão bem sucedida da operação de escala, terá de reequilibrar a topologia.

    O reequilíbrio pode ser realizado de duas formas:

  * UI web tempestade
  * Ferramenta de interface de linha de comando (CLI)

    Consulte a [documentação](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) da Tempestade Apache para mais detalhes.

    O UI web storm está disponível no cluster HDInsight:

    ![Reequilíbrio da escala de tempestade HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Aqui está um exemplo de comando CLI para reequilibrar a topologia da tempestade:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Como escalar com segurança um aglomerado

### <a name="scale-down-a-cluster-with-running-jobs"></a>Escalde um cluster com empregos em execução

Para evitar que os seus trabalhos de corrida falhem durante uma operação de redução de escala, pode tentar três coisas:

1. Espere que os trabalhos completem antes de reduzir o seu aglomerado.
1. Termine manualmente os trabalhos.
1. Reenvie os postos de trabalho após a conclusão da operação de escala.

Para ver uma lista de trabalhos pendentes e em execução, pode utilizar o **YARN Resource Manager UI,** seguindo estes passos:

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster.  Consulte [a Lista e mostre](./hdinsight-administer-use-portal-linux.md#showClusters) os agrupamentos para obter as instruções. O cluster é aberto numa nova página do portal.
2. Da vista principal, navegue até **aos dashboards do Cluster** > casa **ambari**. Insira as credenciais do seu cluster.
3. A partir do Ambari UI, selecione **YARN** na lista de serviços no menu à esquerda.  
4. A partir da página YARN, selecione **Quick Links** e paire sobre o nó de cabeça ativo e, em seguida, selecione **ResourceManager UI**.

    ![Apache Ambari liga rapidamente RecursosManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Pode aceder diretamente ao UI do ResourceManager com `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Vê-se uma lista de empregos, juntamente com o seu estado atual. Na imagem, há um trabalho em andamento:

![Aplicações UI do ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Para matar manualmente a aplicação de execução, execute o seguinte comando a partir da concha SSH:

```bash
yarn application -kill <application_id>
```

Por exemplo:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Ficar preso em modo de segurança

Quando reduz um cluster, o HDInsight utiliza interfaces de gestão Apache Ambari para primeiro desativar os nós de trabalhadores extra, que replicam os seus blocos HDFS a outros nós de trabalhadores online. Depois disso, o HDInsight escala com segurança o cluster para baixo. O HDFS entra em modo de segurança durante a operação de escalagem, e é suposto sair assim que a escala estiver terminada. Em alguns casos, no entanto, o HDFS fica preso em modo de segurança durante uma operação de escala devido à sub-replicação do bloco de ficheiros.

Por predefinição, o HDFS é configurado com uma definição `dfs.replication` de 1, que controla quantas cópias de cada bloco de ficheiros estão disponíveis. Cada cópia de um bloco de ficheiros é armazenada num nó diferente do cluster.

Quando o HDFS deteta que o número esperado de cópias de blocos não está disponível, o HDFS entra no modo de segurança e ambari gera alertas. Se o HDFS entrar no modo de segurança para uma operação de escala, mas não conseguir sair do modo de segurança porque o número necessário de nós não é detetado para replicação, o cluster pode ficar preso em modo de segurança.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo quando o modo de segurança é ligado

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Pode rever os registos de nó do nome da pasta `/var/log/hadoop/hdfs/`, perto da altura em que o cluster foi dimensionado, para ver quando entrou no modo de segurança. Os ficheiros de registo são chamados `Hadoop-hdfs-namenode-<active-headnode-name>.*`.

A causa principal dos erros anteriores é que a Hive depende de ficheiros temporários em HDFS enquanto executa consultas. Quando o HDFS entra no modo de segurança, a Hive não pode executar consultas porque não pode escrever para hDFS. Os ficheiros temporários em HDFS estão localizados na unidade local montada nas VMs individuais do nó operário, e replicados entre outros nós operários em três réplicas, no mínimo.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Como evitar que o HDInsight se prenda em modo de segurança

Existem várias formas de evitar que o HDInsight seja deixado em modo de segurança:

* Pare todos os trabalhos da Colmeia antes de reduzir o HDInsight. Alternadamente, agende o processo de redução da escala para evitar conflitos com a gestão de postos de trabalho na Colmeia.
* Limpe manualmente o risco da Hive `tmp` ficheiros de diretório no HDFS antes de reduzir.
* Apenas reduza o HDInsight para três nós de trabalhador, no mínimo. Evite ir tão baixo como um nó de trabalhador.
* Executar o comando para deixar o modo de segurança, se necessário.

As seguintes secções descrevem estas opções.

#### <a name="stop-all-hive-jobs"></a>Pare todos os trabalhos da Colmeia

Pare todos os trabalhos da Colmeia antes de reduzir para um nó de trabalhador. Se a sua carga de trabalho estiver programada, execute a sua redução de escala após o trabalho da Colmeia.

Parar os trabalhos da Colmeia antes de escalonar, ajuda a minimizar o número de ficheiros de risco na pasta TMP (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpe manualmente os ficheiros de risco da Hive

Se a Hive deixou ficheiros temporários, então pode limpar manualmente esses ficheiros antes de se escalonar para evitar o modo de segurança.

1. Verifique qual a localização que está a ser usada para ficheiros temporários da Hive, olhando para a propriedade de configuração `hive.exec.scratchdir`. Este parâmetro está definido dentro `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Pare os serviços da Hive e certifique-se de que todas as consultas e trabalhos estão concluídos.
2. Enumerar o conteúdo do diretório de riscos acima encontrado, `hdfs://mycluster/tmp/hive/` para ver se contém ficheiros:

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

3. Se souber que a Hive acabou com estes ficheiros, pode removê-los. Certifique-se de que a Hive não tem quaisquer dúvidas em execução, procurando na página UI do Yarn ResourceManager.

    Linha de comando de exemplo para remover ficheiros do HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Escala HDInsight para três ou mais nós de trabalhador

Se os seus agrupamentos ficarem presos em modo de segurança frequentemente ao escalonar para menos de três nós de trabalhador, e os passos anteriores não funcionarem, então pode evitar que o seu cluster entre completamente em modo de segurança mantendo pelo menos três nós de trabalhador.

Reter três nós de trabalhador é mais dispendioso do que reduzir para apenas um nó de trabalhador, mas evitará que o seu cluster fique preso em modo de segurança.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Scale HDInsight até um nó de trabalhador

Mesmo quando o aglomerado é reduzido a 1 nó, o nó de trabalhador0 0 ainda sobreviverá. O nó 0 dos trabalhadores nunca pode ser desativado.

#### <a name="run-the-command-to-leave-safe-mode"></a>Executar o comando para deixar o modo de segurança

A opção final é executar o comando do modo de segurança de saída. Se souber que a razão para o HDFS entrar no modo de segurança é devido à sub-replicação do ficheiro Hive, pode executar o seguinte comando para deixar o modo de segurança:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Escalde um cluster Apache HBase

Os servidores da região são automaticamente equilibrados dentro de alguns minutos após completar uma operação de escala. Para equilibrar manualmente os servidores da região, complete os seguintes passos:

1. Ligue-se ao cluster HDInsight utilizando O SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

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
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
