---
title: Alta disponibilidade para Hadoop – Azure HDInsight
description: Saiba como os clusters HDInsight melhoram a confiabilidade e a disponibilidade usando um nó principal adicional. Saiba como isso afeta os serviços do Hadoop, como o Ambari e o Hive, e também como se conectar individualmente a cada nó de cabeçalho usando SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: alta disponibilidade do Hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 8c3e377faef4e18bff01fd7001751d1f1e347b8d
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030880"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Disponibilidade e confiabilidade de clusters de Apache Hadoop no HDInsight

Os clusters HDInsight fornecem dois nós de cabeçalho para aumentar a disponibilidade e a confiabilidade dos serviços de Apache Hadoop e trabalhos em execução.

O Hadoop alcança alta disponibilidade e confiabilidade replicando serviços e dados em vários nós em um cluster. No entanto, as distribuições padrão do Hadoop normalmente têm apenas um único nó de cabeçalho. Qualquer interrupção do nó de cabeçalho único pode fazer com que o cluster pare de funcionar. O HDInsight fornece duas cabeçalho para melhorar a disponibilidade e a confiabilidade do Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Disponibilidade e confiabilidade de nós

Os nós em um cluster HDInsight são implementados usando máquinas virtuais do Azure. As seções a seguir discutem os tipos de nó individuais usados com o HDInsight.

> [!NOTE]  
> Nem todos os tipos de nós são usados para um tipo de cluster. Por exemplo, um tipo de cluster Hadoop não tem nenhum nó Nimbus. Para obter mais informações sobre os nós utilizados pelos tipos de cluster HDInsight, consulte a secção de tipos de Cluster dos [clusters Hadoop baseados em Linux no documento HDInsight.](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)

### <a name="head-nodes"></a>Nós de cabeça

Para garantir a alta disponibilidade dos serviços do Hadoop, o HDInsight fornece dois nós de cabeçalho. Ambos os nós de cabeça estão ativos e em execução no cluster HDInsight simultaneamente. Alguns serviços, como Apache HDFS ou Apache Hadoop YARN, são apenas "ativos" em um nó de cabeçalho em um determinado momento. Outros serviços, como HiveServer2 ou metastore do hive, estão ativos em ambos os nós de cabeçalho ao mesmo tempo.

Para obter os nomes de anfitriões para diferentes tipos de nó no seu cluster, utilize a [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Não associe o valor numérico a se um nó for primário ou secundário. O valor numérico só está presente para fornecer um nome exclusivo para cada nó.

### <a name="nimbus-nodes"></a>Nódeos Nimbus

Os nós Nimbus estão disponíveis com clusters Apache Storm. Os nós Nimbus fornecem funcionalidade semelhante ao JobTracker do Hadoop distribuindo e monitorando o processamento entre nós de trabalho. O HDInsight fornece dois nós Nimbus para clusters Storm

### <a name="apache-zookeeper-nodes"></a>Nós do Apache Zookeeper

Os nódosos [do ZooKeeper](https://zookeeper.apache.org/) são usados para a eleição de líderes de serviços principais em narizes de cabeça. Eles também são usados para garantir que os serviços, os nós de dados (trabalho) e os gateways saibam em qual nó de cabeçalho um serviço mestre está ativo. Por padrão, o HDInsight fornece três nós ZooKeeper.

### <a name="worker-nodes"></a>Nós de trabalho

Os nós de trabalho executam a análise de dados real quando um trabalho é enviado ao cluster. Se um nó de trabalho falhar, a tarefa que ele estava executando será enviada para outro nó de trabalho. Por padrão, o HDInsight cria quatro nós de trabalho. Você pode alterar esse número para atender às suas necessidades durante e após a criação do cluster.

### <a name="edge-node"></a>nó de borda

Um nó de borda não participa ativamente da análise de dados no cluster. Ele é usado por desenvolvedores ou cientistas de dados ao trabalhar com o Hadoop. O nó de borda reside na mesma rede virtual do Azure que os outros nós no cluster e pode acessar diretamente todos os outros nós. O nó de borda pode ser usado sem tirar recursos dos serviços críticos do Hadoop ou trabalhos de análise.

Atualmente, os serviços de ML no HDInsight são o único tipo de cluster que fornece um nó de borda por padrão. Para serviços de ML no HDInsight, o nó de borda é usado para testar o código R localmente no nó antes de enviá-lo ao cluster para processamento distribuído.

Para obter informações sobre a utilização de um nó de borda com outros tipos de cluster, consulte os nós de borda de utilização no documento [HDInsight.](hdinsight-apps-use-edge-node.md)

## <a name="accessing-the-nodes"></a>Acessando os nós

O acesso ao cluster pela Internet é fornecido por meio de um gateway público. O acesso é limitado à conexão com os nós de cabeçalho e, se houver, o nó de borda. O acesso aos serviços em execução nos nós de cabeçalho não é afetado por ter vários nós de cabeçalho. O gateway público roteia solicitações para o nó principal que hospeda o serviço solicitado. Por exemplo, se o Apache Ambari estiver atualmente hospedado no nó principal secundário, o gateway roteará as solicitações de entrada de Ambari para esse nó.

O acesso ao gateway público é limitado às portas 443 (HTTPS), 22 e 23.

|Porta |Descrição |
|---|---|
|443|Usado para acessar o Ambari e outra interface do usuário da Web ou APIs REST hospedadas nos nós de cabeçalho.|
|22|Usado para acessar o nó principal ou o nó de borda primário com SSH.|
|23|Usado para acessar o nó principal secundário com SSH. Por exemplo, `ssh username@mycluster-ssh.azurehdinsight.net` liga-se ao nó principal da cabeça do cluster chamado **mycluster**.|

Para obter mais informações sobre a utilização do SSH, consulte o SSH use com o documento [HDInsight.](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="internal-fully-qualified-domain-names-fqdn"></a>FQDN (nomes de domínio totalmente qualificados) internos

Os nós em um cluster HDInsight têm um endereço IP interno e um FQDN que só podem ser acessados do cluster. Ao acessar serviços no cluster usando o FQDN ou endereço IP interno, você deve usar Ambari para verificar o IP ou FQDN a ser usado ao acessar o serviço.

Por exemplo, o serviço Apache Oozie só pode funcionar com um nó de cabeça, e usar o comando `oozie` de uma sessão SSH requer o URL para o serviço. Essa URL pode ser recuperada de Ambari usando o seguinte comando:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Este comando devolve um valor semelhante ao seguinte, que contém o URL interno para utilizar com o comando `oozie`:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Para obter mais informações sobre o trabalho com a Ambari REST API, consulte [Monitor e Gerencie o HDInsight utilizando a API De REPOUSO Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Acessando outros tipos de nó

Você pode se conectar a nós que não são diretamente acessíveis pela Internet usando os seguintes métodos:

|Método |Descrição |
|---|---|
|SSH|Uma vez conectado a um nó de cabeçalho usando SSH, você pode usar o SSH do nó principal para se conectar a outros nós no cluster. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Túnel SSH|Se precisar acessar um serviço Web hospedado em um dos nós que não está exposto à Internet, você deverá usar um túnel SSH. Para mais informações, consulte o [Utilize um túnel SSH com documento HDInsight.](hdinsight-linux-ambari-ssh-tunnel.md)|
|Rede Virtual do Azure|Se o seu cluster HDInsight fizer parte de uma rede virtual do Azure, qualquer recurso na mesma rede virtual poderá acessar diretamente todos os nós no cluster. Para mais informações, consulte o Plano uma rede virtual para o documento [HDInsight.](hdinsight-plan-virtual-network-deployment.md)|

## <a name="how-to-check-on-a-service-status"></a>Como verificar um status de serviço

Para verificar o status dos serviços que são executados nos nós de cabeçalho, use a interface do usuário da Web do amAmbari ou a API REST do Ambari.

### <a name="ambari-web-ui"></a>Interface do usuário da Web do amAmbari

O Ambari Web UI é visível em `https://CLUSTERNAME.azurehdinsight.net`. Substitua **CLUSTERNAME** pelo nome do cluster. Se solicitado, insira as credenciais de usuário HTTP para o cluster. O nome de utilizador 'HTTP' predefinido é **administrador** e a palavra-passe é a palavra-passe que introduziu ao criar o cluster.

Quando você chegar à página Ambari, os serviços instalados serão listados à esquerda da página.

![Serviços instalados do Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Há uma série de ícones que podem aparecer ao lado de um serviço para indicar o status. Quaisquer alertas relacionados com um serviço podem ser vistos utilizando o link **Alertas** na parte superior da página.  O Ambari oferece vários alertas predefinidos.

Os alertas a seguir ajudam a monitorar a disponibilidade de um cluster:

| Nome do Alerta                               | Descrição                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status do monitor de métrica                    | Esse alerta indica o status do processo de monitor de métricas conforme determinado pelo script de status do monitor.                                                                                   |
| Pulsação do agente Ambari                   | Esse alerta será disparado se o servidor tiver perdido o contato com um agente.                                                                                                                        |
| Processo do servidor ZooKeeper                 | Esse alerta em nível de host será disparado se o processo do servidor ZooKeeper não puder ser determinado como ativo e escutando na rede.                                                               |
| Status do servidor de metadados IOCache           | Esse alerta no nível do host será disparado se o servidor de metadados IOCache não puder ser determinado como ativo e respondendo às solicitações do cliente                                                            |
| Interface do usuário da Web do amJournalNode                       | Esse alerta no nível do host será disparado se a interface do usuário da Web do amJournalNode estiver inacessível.                                                                                                                 |
| Servidor Spark2 thrift                     | Esse alerta em nível de host será disparado se o servidor Spark2 Thrift não puder ser determinado como ativo.                                                                                                |
| Processo do servidor de histórico                   | Esse alerta em nível de host será disparado se o processo do servidor de histórico não puder ser estabelecido para estar ativo e escutando na rede.                                                                |
| IU da Web do servidor de histórico                    | Esse alerta no nível do host será disparado se a interface do usuário da Web do servidor de histórico estiver inacessível.                                                                                                              |
| `ResourceManager` Web UI                   | Este alerta de nível de hospedeiro é desencadeado se o `ResourceManager` Web UI for inacessível.                                                                                                             |
| Resumo de integridade do NodeManager               | Esse alerta de nível de serviço é disparado se houver NodeManagers não íntegros                                                                                                                    |
| Linha do tempo de aplicativo da Web                      | Esse alerta no nível do host será disparado se a interface do usuário da Web do servidor de linha do tempo do aplicativo estiver inacessível.                                                                                                         |
| Resumo de integridade do datanode                  | Esse alerta de nível de serviço é disparado se houver datanodes não íntegros                                                                                                                       |
| Interface do usuário da Web do amNameNode                          | Esse alerta no nível do host será disparado se a interface do usuário da Web do amNameNode estiver inacessível.                                                                                                                    |
| Processo do controlador de failover ZooKeeper    | Esse alerta em nível de host será disparado se o processo do controlador de failover ZooKeeper não puder ser confirmado para estar ativo e escutando na rede.                                                   |
| Interface do usuário da Web do Oozie Server                      | Esse alerta no nível do host será disparado se a interface do usuário da Web do Oozie Server estiver inacessível.                                                                                                                |
| Status do servidor Oozie                      | Esse alerta no nível do host será disparado se o servidor Oozie não puder ser determinado como ativo e respondendo às solicitações do cliente.                                                                      |
| Processo de metastore do hive                   | Esse alerta em nível de host será disparado se o processo de metastore do hive não puder ser determinado como ativo e escutando na rede.                                                                 |
| Processo de HiveServer2                      | Esse alerta no nível do host será disparado se o HiveServer não puder ser determinado como ativo e respondendo às solicitações do cliente.                                                                        |
| Status do servidor WebHCat                    | Este alerta de nível de hospedeiro é desencadeado se o estado do servidor `templeton` não for saudável.                                                                                                            |
| Porcentagem de servidores ZooKeeper disponíveis      | Esse alerta será disparado se o número de servidores ZooKeeper no cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo do ZooKeeper.     |
| Servidor Spark2 Livy                       | Esse alerta no nível do host será disparado se o servidor Livy2 não puder ser determinado como ativo.                                                                                                        |
| Servidor de histórico do Spark2                    | Esse alerta em nível de host será disparado se o servidor de histórico Spark2 não puder ser determinado como ativo.                                                                                               |
| Processo do coletor de métricas                | Esse alerta será disparado se o coletor de métricas não puder ser confirmado para estar ativo e escutando na porta configurada por um número de segundos igual ao limite.                                 |
| Coletor de métricas – processo de HBase Master | Esse alerta será disparado se os processos do HBase Master do coletor de métricas não puderem ser confirmados como ativos e escutando na rede o limite crítico configurado, fornecido em segundos. |
| Percentual de monitores de métricas disponíveis       | Esse alerta será disparado se um percentual de processos de monitor de métricas não estiver ativo e escutando na rede para o aviso e os limites críticos configurados.                             |
| Porcentagem de NodeManagers disponíveis           | Esse alerta será disparado se o número de NodeManagers inativos no cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo do NodeManager.        |
| Integridade do NodeManager                       | Esse alerta no nível do host verifica a propriedade de integridade do nó disponível no componente NodeManager.                                                                                              |
| Interface do usuário da Web do NodeManager                       | Esse alerta em nível de host será disparado se a interface do usuário da Web NodeManager não estiver acessível.                                                                                                                 |
| Integridade de alta disponibilidade do NameNode        | Esse alerta de nível de serviço será disparado se o NameNode ativo ou o NameNode em espera não estiverem em execução.                                                                                     |
| Processo do datanode                         | Esse alerta em nível de host será disparado se os processos individuais de datanode não puderem ser estabelecidos para serem ativados e escutando na rede.                                                         |
| Interface do usuário da Web do datanode                          | Esse alerta no nível do host será disparado se a interface do usuário da Web do datanode estiver inacessível.                                                                                                                    |
| Porcentagem de JournalNodes disponíveis           | Esse alerta será disparado se o número de JournalNodes inativos no cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo do JournalNode.        |
| Porcentagem de datanodes disponíveis              | Esse alerta será disparado se o número de datanodes inativos no cluster for maior que o limite crítico configurado. Ele agrega os resultados de verificações de processo de datanode.              |
| Status do servidor Zeppelin                   | Esse alerta no nível do host será disparado se o servidor Zeppelin não puder ser determinado como ativo e respondendo às solicitações do cliente.                                                                   |
| Processo interativo do HiveServer2          | Esse alerta no nível do host será disparado se o HiveServerInteractive não puder ser determinado como ativo e respondendo às solicitações do cliente.                                                             |
| Aplicativo LLAP                         | Esse alerta será disparado se o aplicativo LLAP não puder ser determinado como ativo e respondendo a solicitações.                                                                                    |

Você pode selecionar cada serviço para exibir mais informações sobre ele.

Enquanto a página de serviço fornece informações sobre o status e a configuração de cada serviço, ela não fornece informações sobre em qual nó de cabeçalho o serviço está sendo executado. Para ver esta informação, utilize o link **Hosts** na parte superior da página. Essa página exibe os hosts no cluster, incluindo os nós de cabeçalho.

![Lista de hosts do Apache Ambari cabeçalho](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Selecionar o link para um dos nós de cabeçalho exibe os serviços e os componentes em execução nesse nó.

![Status do componente Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Para obter mais informações sobre a utilização de Ambari, consulte [monitor e gerencie o HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>API REST do Ambari

A API REST do Ambari está disponível pela Internet. O gateway público do HDInsight manipula solicitações de roteamento para o nó principal que está hospedando a API REST no momento.

Você pode usar o seguinte comando para verificar o estado de um serviço por meio da API REST do amAmbari:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Substitua a **palavra-passe** com a palavra-passe da conta http (administradora).
* Substitua **CLUSTERNAME** pelo nome do cluster.
* Substitua o **SERVICENAME** pelo nome do serviço que pretende verificar o estado.

Por exemplo, para verificar o estado do serviço **HDFS** num cluster denominado **mycluster,** com uma palavra-passe de **senha,** utilizaria o seguinte comando:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

A resposta é semelhante ao JSON a seguir:

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

O URL diz-nos que o serviço está atualmente a funcionar num nó de cabeça chamado **mycluster.wutj3h4ic1zejluqhxzvckxq0g**.

O Estado diz-nos que o serviço está a funcionar, ou **OSTARTED.**

Se você não souber quais serviços estão instalados no cluster, poderá usar o seguinte comando para recuperar uma lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Para obter mais informações sobre o trabalho com a Ambari REST API, consulte [Monitor e Gerencie o HDInsight utilizando a API De REPOUSO Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Componentes de serviço

Os serviços podem conter componentes dos quais você deseja verificar o status individualmente. Por exemplo, HDFS contém o componente NameNode. Para exibir informações sobre um componente, o comando seria:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Se você não souber quais componentes são fornecidos por um serviço, poderá usar o seguinte comando para recuperar uma lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Como acessar arquivos de log nos nós de cabeçalho

### <a name="ssh"></a>SSH

Enquanto está ligado a um nó de cabeça através do SSH, os ficheiros de registo podem ser encontrados em **/var/log**. Por exemplo, **/var/log/hadoop-fios/fios** contêm registos para o ARN.

Cada nó de cabeçalho pode ter entradas de log exclusivas, portanto, você deve verificar os logs em ambos.

### <a name="sftp"></a>SFTP

Você também pode se conectar ao nó principal usando o SSH protocolo FTP ou o protocolo FTP seguro (SFTP) e baixar os arquivos de log diretamente.

Semelhante ao uso de um cliente SSH, ao se conectar ao cluster, você deve fornecer o nome da conta de usuário SSH e o endereço SSH do cluster. Por exemplo, `sftp username@mycluster-ssh.azurehdinsight.net`. Forneça a palavra-passe para a conta quando solicitado ou forneça uma chave pública utilizando o parâmetro `-i`.

Uma vez ligado, é-lhe apresentado um `sftp>`. Nesse prompt, você pode alterar os diretórios, carregar e baixar arquivos. Por exemplo, os seguintes comandos mudam de diretórios para o diretório **/var/log/hadoop/hdfs** e, em seguida, descarregue todos os ficheiros no diretório.

    cd /var/log/hadoop/hdfs
    get *

Para obter uma lista de comandos disponíveis, introduza `help` no `sftp>` pronta.

> [!NOTE]  
> Também há interfaces gráficas que permitem visualizar o sistema de arquivos quando conectado usando SFTP. Por exemplo, o [MobaXTerm](https://mobaxterm.mobatek.net/) permite-lhe navegar no sistema de ficheiros utilizando uma interface semelhante ao Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Para acessar os arquivos de log usando o Ambari, você deve usar um túnel SSH. As interfaces da Web para os serviços individuais não são expostas publicamente na Internet. Para obter informações sobre a utilização de um túnel SSH, consulte o documento de [túnel SSH de utilização.](hdinsight-linux-ambari-ssh-tunnel.md)

Na interface do usuário da Web do amAmbari, selecione o serviço para o qual você deseja exibir os logs (por exemplo, YARN). Em seguida, utilize **ligações rápidas** para selecionar qual o nó de cabeça para ver os registos.

![Usando links rápidos para exibir logs](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Como configurar o tamanho do nó

O tamanho de um nó só pode ser selecionado durante a criação do cluster. Pode encontrar uma lista dos diferentes tamanhos vm disponíveis para HDInsight na página de [preços HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Ao criar um cluster, você pode especificar o tamanho dos nós. As seguintes informações fornecem orientações sobre como especificar o tamanho utilizando o [portal Azure,](https://portal.azure.com/) [o módulo Azure PowerShell Az](/powershell/azureps-cmdlets-docs)e o [Azure CLI:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

* **Portal Azure**: Ao criar um cluster, pode definir o tamanho dos nós utilizados pelo cluster:

    ![Imagem do assistente de criação de cluster com seleção de tamanho de nó](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: Ao utilizar o comando [`az hdinsight create`,](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) pode definir o tamanho da cabeça, dos pés de trabalho e do ZooKeeper utilizando os parâmetros `--headnode-size`, `--workernode-size`e `--zookeepernode-size`.

* **Azure PowerShell**: Ao utilizar o cmdlet [New-AzHDInsightCluster,](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) pode definir o tamanho dos nós da cabeça, do trabalhador e do ZooKeeper utilizando os `-HeadNodeSize`, `-WorkerNodeSize`e `-ZookeeperNodeSize` parâmetros.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Referência de repouso Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instale e configure o Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Instale e configure o módulo Azure PowerShell Az](/powershell/azure/overview)
* [Gerir o HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
