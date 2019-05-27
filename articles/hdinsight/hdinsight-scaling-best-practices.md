---
title: Tamanhos de cluster de dimensionamento - Azure HDInsight
description: Dimensione um cluster de HDInsight do Azure de forma elástica para corresponder à sua carga de trabalho.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 622261d0f7e602635aa6a638357278a9c63a6ecd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990542"
---
# <a name="scale-hdinsight-clusters"></a>Dimensionar clusters do HDInsight

HDInsight fornece elasticidade, dando-lhe a opção de aumentar verticalmente e reduzir verticalmente o número de nós de trabalho nos seus clusters. Este elasticidade permite-lhe diminuir um cluster após horas ou no fim de semana e expanda-o durante picos de procura de negócios.

Se tiver o processamento em lotes periódicas, pode ser aumentado o cluster de HDInsight alguns minutos antes dessa operação, para que o cluster tem memória suficiente e a potência da CPU.  Mais tarde, depois do processamento é concluído e uso novamente a ficar inativo, pode reduzir verticalmente o cluster do HDInsight para menos nós de trabalho.

Pode dimensionar um cluster manualmente usando um dos métodos descritos abaixo ou utilize [dimensionamento automático](hdinsight-autoscale-clusters.md) opções para fazer o sistema automaticamente ou reduza verticalmente em resposta a CPU, memória e outras métricas.

## <a name="utilities-to-scale-clusters"></a>Utilitários para dimensionar clusters

A Microsoft fornece os seguintes utilitários para dimensionar clusters:

|Utilitário | Descrição|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Conjunto AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) - ClusterName \<nome do Cluster > - TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Conjunto AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<nome do Cluster > - TargetInstanceCount \<NewSize >|
|[CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [Redimensionar AZ hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) – grupo de recursos \<grupo de recursos >-name \<nome do Cluster > – contagem de instâncias de destino \<NewSize >|
|[CLI do Azure](hdinsight-administer-use-command-line.md)|o Azure hdinsight cluster redimensionamento \<clusterName > \<contagem de instâncias de destino > |
|[Portal do Azure](https://portal.azure.com)|Abra o painel de cluster do HDInsight, selecione **tamanho do Cluster** no menu do lado esquerdo, em seguida, no painel de tamanho de Cluster, escreva o número de nós de trabalho e selecione guardar.|  

![Cluster de dimensionamento](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Utilizar qualquer um destes métodos, pode dimensionar o cluster do HDInsight ou reduzir verticalmente dentro de minutos.

> [!IMPORTANT]  
> * A CLI clássica do Azure foi preterida e só deve ser utilizada com o modelo de implementação clássica. Para todas as outras implementações, utilize o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * O módulo PowerShell AzureRM foi preterido.  Utilize o [módulo Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) sempre que possível.

## <a name="impact-of-scaling-operations"></a>Impacto das operações de dimensionamento

Quando **adicionar** nós para em execução HDInsight cluster (aumentar verticalmente), todas as tarefas em execução ou pendentes não serão afetados. Novas tarefas podem ser submetidas com segurança enquanto o processo de dimensionamento está em execução. Se a operação de dimensionamento falhar por algum motivo, a falha será manipulada para deixar o seu cluster num estado funcional.

Se **remover** nós (reduzir verticalmente), qualquer vontade de tarefas pendentes ou em execução falharem quando a operação de dimensionamento for concluída. Esta falha é devido a alguns dos serviços reiniciar durante o processo de dimensionamento. Também é um risco de que o seu cluster pode obter bloqueado no modo de segurança durante uma operação de dimensionamento de manual.

## <a name="how-to-safely-scale-down-a-cluster"></a>Como com segurança reduzir verticalmente um cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Reduzir verticalmente um cluster com tarefas em execução

Para evitar que as tarefas em execução falhar durante a operação de redução de dimensionamento, pode experimentar três coisas:

1. Aguarde que as tarefas esteja concluída antes de reduzir verticalmente o cluster.
1. Termina manualmente as tarefas.
1. Volte a submeter as tarefas após tem concluíram a operação de dimensionamento.

Para ver uma lista de pendentes e tarefas em execução, pode usar o YARN **IU de ResourceManager**, seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. A partir do lado esquerdo, navegue para **todos os serviços** > **análise** > **Clusters do HDInsight**e, em seguida, selecione o cluster.
3. A vista principal, navegue para **dashboards de clusters** > **Ambari doméstica**. Introduza as credenciais de cluster.
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

### <a name="getting-stuck-in-safe-mode"></a>A acumular-se no modo de segurança

Quando reduzir verticalmente um cluster, o HDInsight utiliza interfaces de gerenciamento do Apache Ambari para primeiro desativar os nós de trabalho extra, o qual replicar os seus blocos HDFS para outros nós de trabalho online. Depois disso, o HDInsight aumenta com segurança o cluster diminui. HDFS entra em modo de segurança durante a operação de dimensionamento e deve para aparecer assim que o dimensionamento é concluído. Em alguns casos, no entanto, HDFS fica preso em modo de segurança durante uma operação de dimensionamento devido a replicação de insuficientemente de blocos de arquivo.

Por predefinição, o HDFS é configurado com um `dfs.replication` definição de 3, que controla o número de cópias de cada bloco do arquivo está disponível. Cada cópia de um bloco de ficheiro é armazenada num nó diferente do cluster.

Quando o HDFS detecta que o número esperado de cópias de bloco não está disponível, HDFS entra em modo seguro e Ambari gera alertas. Se HDFS introduz o modo de segurança numa operação de dimensionamento, mas, em seguida, não pode sair do modo de segurança porque o número necessário de nós não é detetado para a replicação, pode ficar bloqueado, o cluster no modo de segurança.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo, quando o modo de segurança está ativado

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Pode rever os registos de nó de nome do `/var/log/hadoop/hdfs/` pasta, perto da hora quando o cluster foi dimensionado, para ver quando ele introduziu o modo de segurança. Os ficheiros de registo são nomeados `Hadoop-hdfs-namenode-hn0-clustername.*`.

A causa dos erros anteriores é que Hive depende ficheiros temporários no HDFS durante a execução de consultas. Quando o HDFS entra em modo seguro, ramo de registo não é possível executar consultas porque ele não é possível escrever no HDFS. Os arquivos temporários no HDFS estão localizados na unidade local instalado no nó de trabalho individuais VMs e replicados entre os outros nós de trabalho em três réplicas, mínimo.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Como impedir que o HDInsight presos no modo de segurança

Existem várias formas para impedir que o HDInsight a ser deixada no modo de segurança:

* Pare todas as tarefas de ramo de registo antes de dimensionar para baixo do HDInsight. Em alternativa, agende a reduzir verticalmente o processo para evitar em conflito com a execução de tarefas do Hive.
* Limpar manualmente zero do Hive `tmp` ficheiros do diretório hdfs antes de dimensionar para baixo.
* Apenas reduza verticalmente o HDInsight para três nós de trabalho, mínimo. Evite vai como tão baixo como nó de um trabalho.
* Execute o comando sair do modo de segurança, se necessário.

As secções seguintes descrevem estas opções.

#### <a name="stop-all-hive-jobs"></a>Parar todas as tarefas do Hive

Pare todas as tarefas do Hive antes de reduzir verticalmente para o nó de um trabalho. Se estiver agendada a sua carga de trabalho, em seguida, execute o seu verticalmente depois de terminar o trabalho do Hive.

A parar as tarefas do Hive antes de dimensionar, ajuda a minimizar o número de ficheiros de rascunho na pasta tmp (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpar manualmente os ficheiros de rascunho do Hive

Se o Hive tem deixadas para trás ficheiros temporários, em seguida, pode limpar manualmente esses arquivos antes de dimensionar para evitar o modo de segurança.

1. Verifique qual local está a ser utilizado para ficheiros temporários do ramo de registo ao observar o `hive.exec.scratchdir` propriedade de configuração. Este parâmetro estiver definido no `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Parar os serviços do Hive e certifique-se de que todas as consultas e as tarefas são concluídas.
2. Listar o conteúdo do diretório de rascunho encontrados acima, `hdfs://mycluster/tmp/hive/` para ver se ele contém todos os ficheiros:

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

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight de dimensionamento para três ou mais nós de trabalho

Se os clusters de ficar preso no modo de segurança com frequência quando o dimensionamento para baixo para menos de três nós de trabalho e os passos anteriores não funcionarem, em seguida, pode evitar o cluster vai modo de segurança totalmente mantendo a, pelo menos, três nós de trabalho.

Manter os três nós de trabalho é mais dispendioso do que reduzir verticalmente para o nó de trabalho apenas uma, mas irá impedir que o cluster a acumular-se no modo de segurança.

#### <a name="run-the-command-to-leave-safe-mode"></a>Execute o comando sair do modo de segurança

A última opção é a executar o comando de modo de segurança de saída. Se souber que o motivo para o HDFS entrar no modo seguro é devido a replicação de insuficientemente de ficheiros do ramo de registo, pode executar o seguinte comando para sair do modo de segurança:


```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Reduzir verticalmente um cluster do Apache HBase

Servidores de região são balanceadas automaticamente dentro de alguns minutos depois de concluir uma operação de dimensionamento. Para balancear manualmente os servidores de região, conclua os seguintes passos:

1. Ligue ao cluster do HDInsight através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Inicie a shell de HBase:

    ```bash
    hbase shell
    ```

3. Utilize o seguinte comando para balancear manualmente os servidores de região:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Passos Seguintes

* [Dimensionar automaticamente os clusters do HDInsight do Azure](hdinsight-autoscale-clusters.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Dimensionar clusters](hdinsight-administer-use-portal-linux.md#scale-clusters)
