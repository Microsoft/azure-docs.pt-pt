---
title: Tamanhos de cluster de dimensionamento - Azure HDInsight
description: Dimensione um cluster do HDInsight para a sua carga de trabalho.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: ashish
ms.openlocfilehash: e8a85401c0c7282d64ebcbe2f9180f25f36f7289
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108159"
---
# <a name="scale-hdinsight-clusters"></a>Dimensionar clusters do HDInsight

HDInsight fornece elasticidade, dando-lhe a opção de aumentar verticalmente e reduzir verticalmente o número de nós de trabalho nos seus clusters. Isto permite-lhe diminuir um cluster após horas ou no fim de semana e expanda-o durante picos de procura de negócios.

Por exemplo, se tiver algum processamento em lotes que ocorre uma vez por dia ou uma vez por mês, o cluster do HDInsight pode ser aumentado alguns minutos antes que o evento agendado para que haja memória suficiente e poder de computação de CPU.  Mais tarde, depois do processamento é concluído e uso novamente a ficar inativo, pode reduzir verticalmente o cluster do HDInsight para menos nós de trabalho.

## <a name="utilities-to-scale-clusters"></a>Utilitários para dimensionar clusters

A Microsoft fornece os seguintes utilitários para dimensionar clusters:

|Utilitário | Descrição|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)|[Conjunto AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) - ClusterName \<nome do Cluster > - TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm/overview) |[Conjunto AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<nome do Cluster > - TargetInstanceCount \<NewSize >|
|[CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)|[Redimensionar AZ hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) – grupo de recursos \<grupo de recursos >-name \<nome do Cluster > – contagem de instâncias de destino \<NewSize >|
|[CLI clássica do Azure](hdinsight-administer-use-command-line.md)|o Azure hdinsight cluster redimensionamento \<clusterName > \<contagem de instâncias de destino >|
|[Portal do Azure](https://portal.azure.com)|Abra o painel de cluster do HDInsight, selecione **tamanho do Cluster** no menu do lado esquerdo, em seguida, no painel de tamanho de Cluster, escreva o número de nós de trabalho e selecione guardar.|  

![Dimensionar o cluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Utilizar qualquer um destes métodos, pode dimensionar o cluster do HDInsight ou reduzir verticalmente dentro de minutos.

> [!IMPORTANT]  
> * A CLI clássica do Azure foi preterida e só deve ser utilizada com o modelo de implementação clássica. Para todas as outras implementações, utilize o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * O módulo PowerShell AzureRM foi preterido.  Utilize o [módulo Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) sempre que possível.

## <a name="scaling-impacts-on-running-jobs"></a>Dimensionamento impactos sobre tarefas em execução

Quando **adicionar** nós ao cluster do HDInsight em execução, todas as tarefas em execução ou pendentes não serão afetadas. Além disso, novas tarefas podem ser com segurança submetidas enquanto o processo de dimensionamento está em execução. Se as operações de dimensionamento falhem por algum motivo, a falha é corretamente processada, mantendo o cluster num estado funcional.

No entanto, se estiver reduzir verticalmente o cluster mediante **remover** nós, todas as tarefas em execução ou pendentes irão falhar quando a operação de dimensionamento for concluída. Esta falha é devido a alguns dos serviços reiniciar durante o processo.

Para resolver este problema, pode aguardar que as tarefas a concluir antes de dimensionar para baixo do seu cluster, manualmente terminar as tarefas ou volte a submeter as tarefas após tem concluíram a operação de dimensionamento.

Para ver uma lista de pendentes e tarefas em execução, pode utilizar a IU de ResourceManager do YARN, seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. A partir do lado esquerdo, navegue para **todos os serviços** > **análise** > **Clusters do HDInsight**e, em seguida, selecione o cluster.
3. A vista principal, navegue para **dashboards de clusters** > **Ambari doméstica**. Introduza as credenciais de início de sessão do cluster.
4. Na IU do Ambari, selecione **YARN** na lista de serviços no menu do lado esquerdo.  
5. Na página do YARN, selecione **ligações rápidas** e coloque o cursor sobre o nó principal do Active Directory, em seguida, selecione **IU de ResourceManager**.

    ![IU de ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Pode aceder diretamente a IU de ResourceManager com `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

É apresentada uma lista de tarefas, juntamente com o respetivo estado atual. Na captura de ecrã, existe uma tarefa atualmente em execução:

![Aplicativos de IU de ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Para eliminar manualmente esse aplicativo em execução, execute o seguinte comando a partir da shell SSH:

```bash
yarn application -kill <application_id>
```

Por exemplo:

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-apache-hbase-cluster"></a>Reequilibrar um cluster do Apache HBase

Servidores de região são balanceadas automaticamente dentro de alguns minutos após a conclusão da operação de dimensionamento. Para balancear manualmente os servidores de região, utilize os seguintes passos:

1. Ligue ao cluster do HDInsight através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Inicie a shell de HBase:

        hbase shell

3. Utilize o seguinte comando para balancear manualmente os servidores de região:

        balancer

## <a name="scale-down-implications"></a>Reduzir verticalmente implicações

Como mencionado anteriormente, as tarefas em execução ou pendentes são terminadas após a conclusão de uma reduzir verticalmente a operação. No entanto, existem outras implicações potenciais para dimensionamento inferior que possam ocorrer.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>HDInsight nó de nome permanece no modo de segurança depois de reduzir verticalmente

Se reduzir o seu cluster para baixo para o mínimo de nó de um trabalho, Apache HDFS poderá ficar bloqueada no modo de segurança, quando nós de trabalho são reiniciadas devido a aplicação de patches, ou imediatamente após a operação de dimensionamento.

A principal causa isso é que o ramo de registo utiliza algumas `scratchdir` ficheiros e, por predefinição espera três réplicas de cada bloco, mas há apenas uma das réplicas possíveis se reduzir verticalmente para o nó de trabalho de um mínimo. Como uma conseqüência, os ficheiros a `scratchdir` tornam-se *under-replicados*. Isso poderia causar HDFS para se manter em modo de segurança quando os serviços são reiniciados após a operação de dimensionamento.

Quando acontece um reduzir verticalmente tentativa, o HDInsight baseia-se após as interfaces de gerenciamento do Apache Ambari para primeiro desativar os nós de trabalho extra indesejados, o qual replicam seus blocos HDFS para outros nós de trabalho online, e, em seguida, dimensionar o cluster com segurança para baixo. HDFS entra num modo de segurança durante a janela de manutenção e deve para aparecer assim que o dimensionamento é concluído. Neste momento é que pode ficar bloqueado, HDFS no modo de segurança.

HDFS está configurado com um `dfs.replication` definição de 3. Assim, os blocos dos ficheiros de rascunho são under-replicados sempre que há menos de três nós de trabalho online, porque não existem não as esperado três cópias de cada bloco de ficheiro disponível.

Pode executar um comando para HDFS sair do modo de segurança. Por exemplo, se sabe que a única razão pela qual o modo seguro está ativado é porque os ficheiros temporários são under-replicados, pode deixar com segurança modo de segurança. Isso ocorre porque os arquivos de under-replicados são arquivos de rascunho temporários do Hive.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Depois de sair do modo de segurança, manualmente pode remover os ficheiros temporários, ou esperar para o Hive, eventualmente, limpá-los automaticamente.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo, quando o modo de segurança está ativado

* H070 não é possível abrir sessão do Hive. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Não é possível criar o diretório** /tmp/hive/hive/819c215c-6d 87-4311-97 c 8-4f0b9d2adcf0. **Nó de nome está no modo de segurança**. Os blocos comunicados 75 precisa 12 blocos adicionais para atingir o limiar 0.9900 de blocos total 87. O número de datanodes em direto 10 atingiu o número mínimo 0. Modo de segurança será desativado automaticamente assim que os limiares forem atingidos.

* Bases de dados de mostrar H100 não é possível submeter a instrução: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: Ligar ao hn0-clustername.servername.internal.cloudapp.net:10001 [hn0 clustername.servername. Falha de Internal.cloudapp.NET/1.1.1.1]: **Ligação recusada**

* Não H020 foi possível estabelecer a ligação para o .net hn0 hdisrv.servername.bx.internal.cloudapp: 10001: org.apache.thrift.transport.TTransportException: Não foi possível criar a ligação de http para http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Ligar ao hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] falhada: Ligação recusada: org.apache.thrift.transport.TTransportException: Não foi possível criar a ligação de http para http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Ligar ao hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] falhada: **Ligação recusada**

* A partir de registos do Hive: WARN [principal]: servidor. HiveServer2 (HiveServer2.java:startHiveServer2(442)) – erro ao iniciar o HiveServer2 na tentativa de 21, novamente em 60 segundos java.lang.RuntimeException: Erro ao aplicar a política de autorização na configuração do hive: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Não é possível criar o diretório** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Nó de nome está no modo de segurança**.
    Os blocos comunicados 0 precisa blocos adicionais de 9 para atingir o limiar 0.9900 dos blocos total de 9.
    O número de datanodes em direto 10 atingiu o número mínimo 0. **Modo de segurança será desativado automaticamente assim que os limiares forem atingidos**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Pode rever os registos de nó de nome do `/var/log/hadoop/hdfs/` pasta, perto da hora quando o cluster foi dimensionado, para ver quando ele introduziu o modo de segurança. Os ficheiros de registo são nomeados `Hadoop-hdfs-namenode-hn0-clustername.*`.

A causa dos erros anteriores é que Hive depende ficheiros temporários no HDFS durante a execução de consultas. Quando o HDFS entra em modo seguro, ramo de registo não é possível executar consultas porque ele não é possível escrever no HDFS. Os arquivos temporários no HDFS estão localizados na unidade local instalado no nó de trabalho individuais VMs e replicados entre os outros nós de trabalho em três réplicas, mínimo.

O `hive.exec.scratchdir` parâmetro no Hive é configurado no `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Ver o estado de funcionamento e o estado do seu sistema de ficheiros HDFS

Pode ver um relatório de estado de cada nó de nome para ver se nós estão em modo de segurança. Para ver o relatório, o SSH para cada nó principal e execute o seguinte comando:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Modo de segurança desativada](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]  
> O `-D` comutador é necessário porque o sistema de ficheiros predefinido no HDInsight é o armazenamento do Azure ou o armazenamento do Azure Data Lake. `-D` Especifica que os comandos são executados no sistema de ficheiros HDFS local.

Em seguida, pode ver um relatório que mostra os detalhes do Estado do HDFS:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Este comando resulta na seguinte num cluster de bom estado de funcionamento em que todos os blocos são replicados para o grau esperado:

![Modo de segurança desativada](./media/hdinsight-scaling-best-practices/report.png)

HDFS suporta o `fsck` bloqueia o comando para verificar inconsistências com vários arquivos, como em falta para um ficheiro ou blocos under-replicados. Para executar o `fsck` comando contra o `scratchdir` ficheiros (disco temporário de rascunho):

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Quando executado num sistema de ficheiros HDFS bom estado de funcionamento com nenhuma blocos under-replicados, pode ver um resultado semelhante ao seguinte:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

Por outro lado, quando o `fsck` comando é executado num sistema de ficheiros HDFS com alguns blocos under-replicados, o resultado é semelhante ao seguinte:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

Também pode ver o estado HDFS na IU do Ambari, selecionando o **HDFS** serviço à esquerda, ou com `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Estado do HDFS do Ambari](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Também poderá ver uma ou mais erros críticos no NameNodes Active Directory ou em modo de espera. Para ver o estado de funcionamento de blocos de NameNode, selecione a ligação de NameNode junto ao alerta.

![O estado de funcionamento do NameNode blocos](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

A limpeza dos ficheiros de rascunho, que remove os erros de replicação de bloco, o SSH em cada nó de avançar e execute o seguinte comando:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]  
> Este comando pode quebrar Hive se algumas tarefas ainda estão em execução.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Como impedir que o HDInsight presos no modo de segurança devido a blocos under-replicados

Existem várias formas para impedir que o HDInsight a ser deixada no modo de segurança:

* Pare todas as tarefas de ramo de registo antes de dimensionar para baixo do HDInsight. Em alternativa, agende a reduzir verticalmente o processo para evitar em conflito com a execução de tarefas do Hive.
* Limpar manualmente zero do Hive `tmp` ficheiros do diretório hdfs antes de dimensionar para baixo.
* Apenas reduza verticalmente o HDInsight para três nós de trabalho, mínimo. Evite vai como tão baixo como nó de um trabalho.
* Execute o comando sair do modo de segurança, se necessário.

As secções seguintes descrevem estas opções.

#### <a name="stop-all-hive-jobs"></a>Parar todas as tarefas do Hive

Pare todas as tarefas do Hive antes de reduzir verticalmente para o nó de um trabalho. Se estiver agendada a sua carga de trabalho, em seguida, execute o seu verticalmente depois de terminar o trabalho do Hive.

Isto ajuda a minimizar o número de rascunho ficheiros na pasta tmp (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpar manualmente os ficheiros de rascunho do Hive

Se o Hive tem deixadas para trás ficheiros temporários, em seguida, pode limpar manualmente esses arquivos antes de dimensionar para evitar o modo de segurança.

1. Parar os serviços do Hive e certifique-se de que todas as consultas e as tarefas são concluídas.

2. Listar o conteúdo do `hdfs://mycluster/tmp/hive/` diretório para ver se ele contém todos os ficheiros:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Aqui está uma saída de exemplo quando existem ficheiros:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Se souber o que Hive é feito com esses arquivos, pode removê-los. Certifique-se de que o ramo de registo não tem quaisquer consultas em execução ao procurar na página da IU de ResourceManager do Yarn.

    Linha de comandos de exemplo para remover ficheiros de HDFS:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>HDInsight de dimensionamento para três nós de trabalho

Se a acumular-se no modo de segurança é um problema persistente e os passos anteriores não estiverem disponíveis, em seguida, talvez queira evitar o problema através do dimensionamento apenas até três nós de trabalho. Talvez isso não seja ideal, devido a restrições de custo, em comparação comparadas a reduzir verticalmente para um nó. No entanto, com apenas um nó de trabalho, HDFS não pode garantir a três réplicas dos dados estão disponíveis para o cluster.

#### <a name="run-the-command-to-leave-safe-mode"></a>Execute o comando sair do modo de segurança

A última opção é a estar atento rara ocasião em que o HDFS entra em modo seguro, em seguida, execute o comando de modo de segurança de saída. Depois de ter determinado que o motivo pelo qual HDFS entrou em modo de segurança é devido aos ficheiros do ramo de registo a ser under-replicada, execute o seguinte comando para sair do modo de segurança:

* HDInsight no Linux:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight no Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Dimensionar clusters](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Manage HDInsight clusters by using the Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight através da IU da Web do Apache Ambari)
