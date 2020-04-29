---
title: Alta disponibilidade para Hadoop - Azure HDInsight
description: Saiba como os clusters HDInsight melhoram a fiabilidade e a disponibilidade utilizando um nó de cabeça adicional. Saiba como isso afeta os serviços hadoop, como Ambari e Hive, bem como como ligar individualmente a cada nó de cabeça usando SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop alta disponibilidade
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 767d87efcf94d720159dcf3b9dc42981ec957ef0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381402"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Disponibilidade e fiabilidade dos clusters Apache Hadoop no HDInsight

Os clusters HDInsight fornecem dois nós de cabeça para aumentar a disponibilidade e fiabilidade dos serviços e postos de trabalho apache hadoop.

Hadoop obtém alta disponibilidade e fiabilidade replicando serviços e dados em vários nós em um cluster. No entanto, as distribuições padrão de Hadoop normalmente têm apenas um nó de cabeça. Qualquer interrupção do nó da cabeça pode fazer com que o aglomerado pare de funcionar. O HDInsight fornece dois headnodes para melhorar a disponibilidade e fiabilidade de Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Disponibilidade e fiabilidade dos nódosos

Os nós de um cluster HDInsight são implementados utilizando máquinas virtuais Azure. As seguintes secções discutem os tipos individuais de nó utilizados com o HDInsight.

> [!NOTE]  
> Nem todos os tipos de nós são usados para um tipo de cluster. Por exemplo, um cluster Hadoop não tem nenhum nó Nimbus. Para obter mais informações sobre os nós utilizados pelos tipos de cluster HDInsight, consulte a secção de tipos de Cluster dos [clusters Hadoop baseados em Linux no documento HDInsight.](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

### <a name="head-nodes"></a>Nódosos da cabeça

Para garantir uma elevada disponibilidade de serviços Hadoop, o HDInsight fornece dois nós de cabeça. Ambos os nós da cabeça estão ativos e em funcionamento dentro do cluster HDInsight simultaneamente. Alguns serviços, como o Apache HDFS ou o Apache Hadoop YARN, só estão 'activos' num nó de cabeça a qualquer momento. Outros serviços, como o HiveServer2 ou o Hive MetaStore, estão ativos em ambos os nós de cabeça ao mesmo tempo.

Para obter os nomes de anfitriões para diferentes tipos de nó no seu cluster, utilize a [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Não associe o valor numérico ao facto de um nó ser primário ou secundário. O valor numérico só está presente para fornecer um nome único para cada nó.

### <a name="nimbus-nodes"></a>Nódeos Nimbus

Os nós de Nimbus estão disponíveis com aglomerados de tempestade Apache. Os nódosos Nimbus fornecem uma funcionalidade semelhante ao Hadoop JobTracker distribuindo e monitorizando o processamento através dos nódos os trabalhadores. HDInsight fornece dois nós Nimbus para aglomerados de tempestade

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper sossega

Os nódosos [do ZooKeeper](https://zookeeper.apache.org/) são usados para a eleição de líderes de serviços principais em narizes de cabeça. Também estão habituados a garantir que os serviços, os nós de dados (trabalhadores) e os gateways sabem em que cabeça um diretor de um serviço principal está ativo. Por padrão, o HDInsight fornece três nós zooKeeper.

### <a name="worker-nodes"></a>Nódosos operários

Os nós dos trabalhadores realizam a análise real de dados quando um trabalho é submetido ao cluster. Se um nó de trabalhador falhar, a tarefa que estava a desempenhar é submetida a outro nó operário. Por padrão, o HDInsight cria quatro nós de trabalhador. Pode alterar este número de acordo com as suas necessidades durante e após a criação do cluster.

### <a name="edge-node"></a>nó de borda

Um nó de borda não participa ativamente na análise de dados dentro do cluster. É usado por desenvolvedores ou cientistas de dados quando trabalha com Hadoop. O nó de borda vive na mesma Rede Virtual Azure que os outros nós do cluster, e pode aceder diretamente a todos os outros nós. O nó de borda pode ser usado sem tirar recursos de serviços hadoop críticos ou trabalhos de análise.

Atualmente, os Serviços ML no HDInsight é o único tipo de cluster que fornece um nó de borda por padrão. Para os serviços ML no HDInsight, o nó de borda é usado código R teste localmente no nó antes de o submeter ao cluster para processamento distribuído.

Para obter informações sobre a utilização de um nó de borda com outros tipos de cluster, consulte os nós de borda de utilização no documento [HDInsight.](hdinsight-apps-use-edge-node.md)

## <a name="accessing-the-nodes"></a>Acesso aos nódosos

O acesso ao cluster através da internet é fornecido através de uma porta de entrada pública. O acesso limita-se à ligação aos nós da cabeça e, se existir, o nó de borda. O acesso aos serviços que correm nos nóóis de cabeça não é afetado por ter vários nódosos de cabeça. As rotas de gateway pública solicitam ao nó de cabeça que acolhe o serviço solicitado. Por exemplo, se apache Ambari está atualmente hospedado no nó de cabeça secundário, as rotas de gateway solicitam ambari para esse nó.

O acesso ao portal público está limitado aos portos 443 (HTTPS), 22 e 23.

|Porta |Descrição |
|---|---|
|443|Usado para aceder a Ambari e outras APIs web UI ou REST hospedados nos nós da cabeça.|
|22|Usado para aceder ao nó da cabeça primária ou nó de borda com SSH.|
|23|Usado para aceder ao nó de cabeça secundário com SSH. Por exemplo, `ssh username@mycluster-ssh.azurehdinsight.net` liga-se ao nó principal da cabeça do cluster chamado **mycluster**.|

Para obter mais informações sobre a utilização do SSH, consulte o SSH use com o documento [HDInsight.](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Nomes de domínio internos totalmente qualificados (FQDN)

Os nós de um cluster HDInsight têm um endereço IP interno e FQDN que só podem ser acedidos a partir do cluster. Ao aceder aos serviços no cluster utilizando o endereço FQDN ou IP interno, deve utilizar a Ambari para verificar o IP ou o FQDN para utilizar ao aceder ao serviço.

Por exemplo, o serviço Apache Oozie só pode funcionar `oozie` num nó de cabeça, e usar o comando de uma sessão SSH requer o URL para o serviço. Este URL pode ser recuperado de Ambari utilizando o seguinte comando:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Este comando devolve um valor semelhante ao seguinte, que `oozie` contém o URL interno para utilizar com o comando:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Para obter mais informações sobre o trabalho com a Ambari REST API, consulte [Monitor e Gerencie o HDInsight utilizando a API De REPOUSO Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Acesso a outros tipos de nó

Pode ligar-se a nós que não são diretamente acessíveis através da internet utilizando os seguintes métodos:

|Método |Descrição |
|---|---|
|SSH|Uma vez ligado a um nó de cabeça utilizando SSH, pode então utilizar SSH do nó da cabeça para se ligar a outros nós do cluster. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Túnel SSH|Se precisa de aceder a um serviço web hospedado num dos nós que não está exposto à internet, tem de utilizar um túnel SSH. Para mais informações, consulte o [Utilize um túnel SSH com documento HDInsight.](hdinsight-linux-ambari-ssh-tunnel.md)|
|Rede Virtual do Azure|Se o seu cluster HDInsight fizer parte de uma Rede Virtual Azure, qualquer recurso na mesma Rede Virtual pode aceder diretamente a todos os nós do cluster. Para mais informações, consulte o Plano uma rede virtual para o documento [HDInsight.](hdinsight-plan-virtual-network-deployment.md)|

## <a name="how-to-check-on-a-service-status"></a>Como verificar o estado do serviço

Para verificar o estado dos serviços que funcionam nos nós da cabeça, utilize o Ambari Web UI ou a Ambari REST API.

### <a name="ambari-web-ui"></a>Ambari Web UI

O Ambari Web UI `https://CLUSTERNAME.azurehdinsight.net`é visível em . Substitua o **CLUSTERNAME** pelo nome do seu cluster. Se solicitado, introduza as credenciais de utilizador http para o seu cluster. O nome de utilizador 'HTTP' predefinido é **administrador** e a palavra-passe é a palavra-passe que introduziu ao criar o cluster.

Quando chega à página ambari, os serviços instalados estão listados à esquerda da página.

![Apache Ambari instalou serviços](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Há uma série de ícones que podem aparecer ao lado de um serviço para indicar o estado. Quaisquer alertas relacionados com um serviço podem ser vistos utilizando o link **Alertas** na parte superior da página.  Ambari oferece vários alertas predefinidos.

Os seguintes alertas ajudam a monitorizar a disponibilidade de um cluster:

| Nome do Alerta                               | Descrição                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado do Monitor Métrico                    | Este alerta indica o estado do processo do Monitor de Métricas determinado pelo script de estado do monitor.                                                                                   |
| Batimento cardíaco do agente Ambari                   | Este alerta é acionado se o servidor tiver perdido o contacto com um agente.                                                                                                                        |
| Processo do servidor zookeeper                 | Este alerta de nível de hospedeiro é desencadeado se o processo do servidor ZooKeeper não puder ser determinado a estar em cima e a ouvir na rede.                                                               |
| Estado do servidor de metadados IOCache           | Este alerta de nível de anfitrião é desencadeado se o Servidor de Metadados IOCache não puder estar determinado a estar de pé e responder aos pedidos do cliente                                                            |
| JournalNode Web UI                       | Este alerta de nível de hospedeiro é desencadeado se o JournalNode Web UI estiver inacessível.                                                                                                                 |
| Servidor Spark2 Thrift                     | Este alerta de nível de hospedeiro é acionado se o Spark2 Thrift Server não puder ser determinado a subir.                                                                                                |
| Processo de servidor de histórico                   | Este alerta de nível de hospedeiro é desencadeado se o processo do History Server não puder ser estabelecido para estar em cima e ouvir na rede.                                                                |
| Web UI do servidor de história                    | Este alerta de nível de anfitrião é desencadeado se o Web UI do Servidor de História estiver inacessível.                                                                                                              |
| `ResourceManager`Web UI                   | Este alerta de nível de `ResourceManager` hospedeiro é acionado se o UI web estiver inacessível.                                                                                                             |
| Resumo da Saúde nodeManager               | Este alerta de nível de serviço é desencadeado se houver NodeManagers pouco saudáveis                                                                                                                    |
| Web UI da linha do tempo da aplicação                      | Este alerta de nível de anfitrião é desencadeado se o Web UI do servidor de linha de linha do tempo da aplicação estiver inacessível.                                                                                                         |
| Resumo da Saúde dataNode                  | Este alerta de nível de serviço é desencadeado se não houver DataNodes saudáveis                                                                                                                       |
| NameNode Web UI                          | Este alerta de nível de hospedeiro é acionado se o NameNode Web UI for inacessível.                                                                                                                    |
| Processo de controlo de failover do ZooKeeper    | Este alerta de nível de hospedeiro é desencadeado se o processo do Controlador de Failover do ZooKeeper não puder ser confirmado para estar em cima e ouvir na rede.                                                   |
| Oozie Server Web UI                      | Este alerta de nível de anfitrião é acionado se o Web UI do servidor Oozie estiver inacessível.                                                                                                                |
| Estado do servidor Oozie                      | Este alerta de nível de anfitrião é desencadeado se o servidor Oozie não puder estar determinado a estar de pé e responder aos pedidos do cliente.                                                                      |
| Processo de Metaloja da Colmeia                   | Este alerta de nível de hospedeiro é desencadeado se o processo da Hive Metastore não puder ser determinado a estar em cima e a ouvir na rede.                                                                 |
| Processo HiveServer2                      | Este alerta de nível de anfitrião é desencadeado se o HiveServer não puder estar determinado a estar de pé e responder aos pedidos do cliente.                                                                        |
| Estado do servidor webhcat                    | Este alerta de nível de `templeton` hospedeiro é acionado se o estado do servidor não estiver saudável.                                                                                                            |
| Por cento dos servidores do ZooKeeper disponíveis      | Este alerta é desencadeado se o número de servidores do ZooKeeper no cluster for maior do que o limiar crítico configurado. Agrega os resultados dos controlos do processo zookeeper.     |
| Spark2 Livy Server                       | Este alerta de nível de hospedeiro é desencadeado se o Servidor Livy2 não puder estar determinado a estar de pé.                                                                                                        |
| Servidor de História Spark2                    | Este alerta de nível de hospedeiro é desencadeado se o Spark2 History Server não puder estar determinado a estar em alta.                                                                                               |
| Processo de Colecionador de Métricas                | Este alerta é desencadeado se o Collector de Métricas não puder ser confirmado para estar em cima e ouvir na porta configurada durante um número de segundos igual ao limiar.                                 |
| Colecionador de Métricas - Processo Master HBase | Este alerta é desencadeado se os processos principais do Coletor de Métricas não puderem ser confirmados para estarem em cima e ouvir na rede o limiar crítico configurado, dado em segundos. |
| Monitores de métricas por cento disponíveis       | Este alerta é desencadeado se uma percentagem dos processos do Monitor de Métricas não estiver em cima e a ouvir na rede os limiares de advertência e críticos configurados.                             |
| Gestores de nó disponíveis           | Este alerta é desencadeado se o número de NodeManagers no cluster for superior ao limiar crítico configurado. Agrega os resultados dos controlos de processo noDeManager.        |
| Saúde nodeManager                       | Este alerta de nível de anfitrião verifica a propriedade de saúde do nó disponível a partir do componente NodeManager.                                                                                              |
| NodeManager Web UI                       | Este alerta de nível de anfitrião é acionado se o NodeManager Web UI estiver inacessível.                                                                                                                 |
| NomeNode Alta Disponibilidade Saúde        | Este alerta de nível de serviço é acionado se o Nome Denome Ativo ou o Nome de Standby Node não estiverem em funcionamento.                                                                                     |
| Processo datanode                         | Este alerta de nível de hospedeiro é desencadeado se os processos de DataNode individuais não puderem ser estabelecidos para estarem em cima e ouvir na rede.                                                         |
| DataNode Web UI                          | Este alerta de nível de hospedeiro é desencadeado se o DataNode Web UI estiver inacessível.                                                                                                                    |
| Por cento de revistas disponíveis           | Este alerta é desencadeado se o número de JournalNodes no cluster for maior do que o limiar crítico configurado. Agrega os resultados dos controlos do processo JournalNode.        |
| Por cento dataNodes disponíveis              | Este alerta é desencadeado se o número de DataNodes no cluster for maior do que o limiar crítico configurado. Agrega os resultados dos controlos do processo DataNode.              |
| Estado do servidor zeppelin                   | Este alerta de nível de anfitrião é desencadeado se o servidor Zeppelin não puder estar determinado a estar de pé e responder aos pedidos do cliente.                                                                   |
| Processo Interativo HiveServer2          | Este alerta de nível de anfitrião é desencadeado se o HiveServerInteractive não puder estar determinado a estar de pé e responder aos pedidos do cliente.                                                             |
| Aplicação LLAP                         | Este alerta é desencadeado se a Aplicação LLAP não puder estar determinada a levantar-se e responder aos pedidos.                                                                                    |

Pode selecionar cada serviço para ver mais informações sobre o mesmo.

Embora a página de serviço forneça informações sobre o estado e configuração de cada serviço, não fornece informações sobre qual o nó de cabeça em que o serviço está em execução. Para ver esta informação, utilize o link **Hosts** na parte superior da página. Esta página exibe anfitriões dentro do cluster, incluindo os nós da cabeça.

![Apache Ambari headnode anfitrião da lista](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

A seleção do link para um dos nóóis de cabeça mostra os serviços e componentes em funcionamento nesse nó.

![Estatuto do componente Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Para obter mais informações sobre a utilização de Ambari, consulte [monitor e gerencie o HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

A Ambari REST API está disponível através da internet. O gateway público HDInsight lida com pedidos de encaminhamento para o nó de cabeça que está atualmente hospedando a API REST.

Pode utilizar o seguinte comando para verificar o estado de um serviço através da API Ambari REST:

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

A resposta é semelhante à seguinte JSON:

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

Se não souber quais os serviços instalados no cluster, pode utilizar o seguinte comando para recuperar uma lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Para obter mais informações sobre o trabalho com a Ambari REST API, consulte [Monitor e Gerencie o HDInsight utilizando a API De REPOUSO Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Componentes de serviço

Os serviços podem conter componentes que deseja verificar o estado individualmente. Por exemplo, o HDFS contém o componente NameNode. Para visualizar informações sobre um componente, o comando seria:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Se não souber quais os componentes fornecidos por um serviço, pode utilizar o seguinte comando para recuperar uma lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Como aceder a ficheiros de registo nos nóóis da cabeça

### <a name="ssh"></a>SSH

Enquanto está ligado a um nó de cabeça através do SSH, os ficheiros de registo podem ser encontrados em **/var/log**. Por exemplo, **/var/log/hadoop-fios/fios** contêm registos para o ARN.

Cada nó de cabeça pode ter entradas de registo únicas, por isso deve verificar os registos em ambos.

### <a name="sftp"></a>SFTP

Também pode ligar-se ao nó principal utilizando o Protocolo de Transferência de Ficheiros SSH ou o Protocolo de Transferência de Ficheiros Seguros (SFTP) e transferir os ficheiros de registo diretamente.

Semelhante à utilização de um cliente SSH, ao ligar-se ao cluster deve fornecer o nome da conta de utilizador SSH e o endereço SSH do cluster. Por exemplo, `sftp username@mycluster-ssh.azurehdinsight.net`. Forneça a palavra-passe para a conta quando solicitado `-i` ou forneça uma chave pública utilizando o parâmetro.

Uma vez ligado, é-lhe apresentado um `sftp>` pedido. A partir desta solicitação, pode alterar diretórios, fazer upload e transferir ficheiros. Por exemplo, os seguintes comandos mudam de diretórios para o diretório **/var/log/hadoop/hdfs** e, em seguida, descarregue todos os ficheiros no diretório.

    cd /var/log/hadoop/hdfs
    get *

Para obter uma lista de `help` comandos disponíveis, insira no `sftp>` momento.

> [!NOTE]  
> Existem também interfaces gráficas que permitem visualizar o sistema de ficheiros quando ligado sFTP. Por exemplo, o [MobaXTerm](https://mobaxterm.mobatek.net/) permite-lhe navegar no sistema de ficheiros utilizando uma interface semelhante ao Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Para aceder aos ficheiros de registo utilizando ambari, deve utilizar um túnel SSH. As interfaces web dos serviços individuais não são expostas publicamente na Internet. Para obter informações sobre a utilização de um túnel SSH, consulte o documento de [túnel SSH de utilização.](hdinsight-linux-ambari-ssh-tunnel.md)

A partir do Ambari Web UI, selecione o serviço para o que pretende visualizar registos (por exemplo, YARN). Em seguida, utilize **ligações rápidas** para selecionar qual o nó de cabeça para ver os registos.

![Usando ligações rápidas para ver registos](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Como configurar o tamanho do nó

O tamanho de um nó só pode ser selecionado durante a criação do cluster. Pode encontrar uma lista dos diferentes tamanhos vm disponíveis para HDInsight na página de [preços HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Ao criar um cluster, pode especificar o tamanho dos nós. As seguintes informações fornecem orientações sobre como especificar o tamanho utilizando o [portal Azure,](https://portal.azure.com/) [o módulo Azure PowerShell Az](/powershell/azureps-cmdlets-docs)e o [Azure CLI:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

* **Portal Azure**: Ao criar um cluster, pode definir o tamanho dos nós utilizados pelo cluster:

    ![Imagem do assistente de criação de cluster com seleção de tamanho do nó](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) Ao utilizar o comando, pode definir o tamanho da cabeça, `--headnode-size`dos `--workernode-size`pés `--zookeepernode-size` de trabalho e do ZooKeeper utilizando os parâmetros e parâmetros.

* **Azure PowerShell**: Ao utilizar o cmdlet [New-AzHDInsightCluster,](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) pode definir o tamanho dos nós `-HeadNodeSize`da `-WorkerNodeSize`cabeça, do trabalhador e do ZooKeeper utilizando os parâmetros e `-ZookeeperNodeSize` parâmetros.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Referência de repouso Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instale e configure o Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Instale e configure o módulo Azure PowerShell Az](/powershell/azure/overview)
* [Gerir o HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
