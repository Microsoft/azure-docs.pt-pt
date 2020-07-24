---
title: Alta disponibilidade para Hadoop - Azure HDInsight
description: Saiba como os clusters HDInsight melhoram a fiabilidade e a disponibilidade utilizando um nó adicional na cabeça. Saiba como isso afeta os serviços hadoop, como a Ambari e a Hive, bem como como conectar-se individualmente a cada nó de cabeça usando SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop alta disponibilidade
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: f97b4877d87acec923812fd0289d5161529dbe7a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081037"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Disponibilidade e fiabilidade dos clusters Apache Hadoop em HDInsight

Os clusters HDInsight fornecem dois nós de cabeça para aumentar a disponibilidade e fiabilidade dos serviços e empregos da Apache Hadoop em funcionamento.

Hadoop obtém alta disponibilidade e fiabilidade replicando serviços e dados em vários nós num cluster. No entanto, as distribuições padrão de Hadoop normalmente têm apenas um nó de cabeça. Qualquer paragem do nó de cabeça única pode fazer com que o cluster deixe de funcionar. O HDInsight fornece dois headnodes para melhorar a disponibilidade e fiabilidade da Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Disponibilidade e fiabilidade dos nosdes

Os nós num cluster HDInsight são implementados usando máquinas virtuais Azure. As secções seguintes discutem os tipos individuais de nós utilizados com HDInsight.

> [!NOTE]  
> Nem todos os tipos de nó são utilizados para um tipo de cluster. Por exemplo, um tipo de cluster Hadoop não tem nenhum nó Nimbus. Para obter mais informações sobre os nós utilizados pelos tipos de cluster HDInsight, consulte a secção de tipos de clusters dos [clusters Hadoop baseados em Create Linux no documento HDInsight.](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

### <a name="head-nodes"></a>Nó de cabeça

Para garantir uma elevada disponibilidade dos serviços Hadoop, o HDInsight fornece dois nós de cabeça. Ambos os nós de cabeça estão ativos e a funcionar dentro do cluster HDInsight simultaneamente. Alguns serviços, como o Apache HDFS ou o Apache Hadoop YARN, são apenas 'ativos' num nó de cabeça a qualquer momento. Outros serviços como HiveServer2 ou Hive MetaStore estão ativos em ambos os nós da cabeça ao mesmo tempo.

Para obter os nomes de anfitriões para diferentes tipos de nó no seu cluster, utilize a [API Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Não associe o valor numérico ao facto de um nó ser primário ou secundário. O valor numérico só está presente para fornecer um nome único para cada nó.

### <a name="nimbus-nodes"></a>Nimbus Nodes

Os nós Nimbus estão disponíveis com aglomerados de tempestade Apache. Os nós Nimbus fornecem uma funcionalidade semelhante ao Hadoop JobTracker distribuindo e monitorizando o processamento através dos nós dos trabalhadores. HDInsight fornece dois nó de Nimbus para aglomerados de tempestade

### <a name="apache-zookeeper-nodes"></a>Nódoa de Zookeeper Apache

Os nós [do ZooKeeper](https://zookeeper.apache.org/) são usados para a eleição de líderes de serviços mestres em nós de cabeça. Também são usados para garantir que os serviços, os nós de dados (trabalhadores) e gateways sabem em que nó de cabeça um serviço principal está ativo. Por padrão, o HDInsight fornece três nós ZooKeeper.

### <a name="worker-nodes"></a>Nódoas operárias

Os nós dos trabalhadores realizam a análise real de dados quando um trabalho é submetido ao cluster. Se um nó de trabalhador falhar, a tarefa que estava a desempenhar é submetida a outro nó de trabalhador. Por padrão, o HDInsight cria quatro nós de trabalhadores. Pode alterar este número de acordo com as suas necessidades durante e após a criação do cluster.

### <a name="edge-node"></a>nó de borda

Um nó de borda não participa ativamente na análise de dados dentro do cluster. É usado por desenvolvedores ou cientistas de dados quando trabalham com Hadoop. O nó de borda vive na mesma Rede Virtual Azure que os outros nós no cluster, e pode aceder diretamente a todos os outros nós. O nó de borda pode ser usado sem tirar recursos de serviços críticos de Hadoop ou trabalhos de análise.

Atualmente, mL Services on HDInsight é o único tipo de cluster que fornece um nó de borda por padrão. Para serviços ML em HDInsight, o nó de borda é usado test R código localmente no nó antes de submetê-lo ao cluster para processamento distribuído.

Para obter informações sobre a utilização de um nó de borda com outros tipos de cluster, consulte os nós de borda de utilização no documento [HDInsight.](hdinsight-apps-use-edge-node.md)

## <a name="accessing-the-nodes"></a>Acedendo aos nosdes

O acesso ao cluster através da internet é fornecido através de uma porta de entrada pública. O acesso limita-se à ligação aos nós da cabeça e, se existir, ao nó de borda. O acesso aos serviços que correm nos nós da cabeça não é afetado por múltiplos nós da cabeça. As vias de entrada pública solicitam ao nó de cabeça que acolhe o serviço solicitado. Por exemplo, se o Apache Ambari está atualmente hospedado no nó de cabeça secundária, as rotas de gateway chegam solicitações de Ambari para esse nó.

O acesso ao portão público está limitado às portas 443 (HTTPS), 22 e 23.

|Porta |Descrição |
|---|---|
|443|Usado para aceder a Ambari e outras UI web ou REST APIs hospedados nos nós da cabeça.|
|22|Usado para aceder ao nó primário da cabeça ou no nó de borda com SSH.|
|23|Usado para aceder ao nó de cabeça secundária com SSH. Por exemplo, `ssh username@mycluster-ssh.azurehdinsight.net` liga-se ao nó primário da cabeça do cluster chamado **mycluster**.|

Para obter mais informações sobre a utilização do SSH, consulte o [SSH de utilização com](hdinsight-hadoop-linux-use-ssh-unix.md) o documento HDInsight.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Nomes de domínios internos totalmente qualificados (FQDN)

Os nós num cluster HDInsight têm um endereço IP interno e FQDN que só podem ser acedidos a partir do cluster. Ao aceder aos serviços no cluster utilizando o endereço FQDN ou IP interno, deverá utilizar Ambari para verificar o IP ou FQDN para utilizar ao aceder ao serviço.

Por exemplo, o serviço Apache Oozie só pode funcionar com um nó de cabeça, e usar o `oozie` comando de uma sessão de SSH requer o URL para o serviço. Este URL pode ser recuperado de Ambari utilizando o seguinte comando:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Este comando devolve um valor semelhante ao seguinte, que contém o URL interno para usar com o `oozie` comando:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Para obter mais informações sobre o trabalho com a API Ambari REST, consulte [Monitor e Manage HDInsight utilizando a API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Aceder a outros tipos de nó

Pode ligar-se a nós que não são acessíveis diretamente através da internet utilizando os seguintes métodos:

|Método |Descrição |
|---|---|
|SSH|Uma vez ligado a um nó de cabeça utilizando SSH, pode então utilizar SSH do nó da cabeça para ligar a outros nós no cluster. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Túnel SSH|Se precisar de aceder a um serviço web hospedado num dos nós que não está exposto à internet, deve utilizar um túnel SSH. Para obter mais informações, consulte o [Use um túnel SSH com documento HDInsight.](hdinsight-linux-ambari-ssh-tunnel.md)|
|Rede Virtual do Azure|Se o seu cluster HDInsight fizer parte de uma Rede Virtual Azure, qualquer recurso na mesma Rede Virtual pode aceder diretamente a todos os nós do cluster. Para obter mais informações, consulte o Plano de Uma rede virtual para o documento [HDInsight.](hdinsight-plan-virtual-network-deployment.md)|

## <a name="how-to-check-on-a-service-status"></a>Como verificar o estado de serviço

Para verificar o estado dos serviços que funcionam nos nós da cabeça, utilize o Ambari Web UI ou a API Ambari REST.

### <a name="ambari-web-ui"></a>Ambari Web UI

O Ambari Web UI é visível em `https://CLUSTERNAME.azurehdinsight.net` . Substitua **o CLUSTERNAME** pelo nome do seu cluster. Se solicitado, insira as credenciais de utilizador HTTP para o seu cluster. O nome de utilizador HTTP predefinido é **administrador** e a palavra-passe é a palavra-passe que introduziu ao criar o cluster.

Quando chega à página Ambari, os serviços instalados estão listados à esquerda da página.

![Serviços instalados apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Há uma série de ícones que podem aparecer ao lado de um serviço para indicar o estado. Quaisquer alertas relacionados com um serviço podem ser vistos usando o link **Alertas** no topo da página.  Ambari oferece vários alertas predefinidos.

Os seguintes alertas ajudam a monitorizar a disponibilidade de um cluster:

| Nome do Alerta                               | Descrição                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado do monitor métrico                    | Este alerta indica o estado do processo do Monitor métrico determinado pelo script de estado do monitor.                                                                                   |
| Batimento cardíaco do agente Ambari                   | Este alerta é acionado se o servidor tiver perdido o contacto com um agente.                                                                                                                        |
| Processo do servidor zookeeper                 | Este alerta de nível de anfitrião é acionado se o processo do servidor ZooKeeper não puder ser determinado a estar ligado e a ouvir na rede.                                                               |
| Estado do servidor de metadados ioCache           | Este alerta de nível de anfitrião é desencadeado se o Servidor de Metadados IOCache não puder ser determinado a estar de pé e responder às solicitações dos clientes                                                            |
| JournalNode Web UI                       | Este alerta de nível de anfitrião é acionado se o UI Web JournalNode estiver inacessível.                                                                                                                 |
| Servidor Spark2 Thrift                     | Este alerta de nível de anfitrião é acionado se o Servidor Spark2 Thrift não puder ser determinado a estar de pé.                                                                                                |
| Processo do servidor de história                   | Este alerta de nível de anfitrião é desencadeado se o processo do History Server não puder ser estabelecido para estar ligado e ouvir na rede.                                                                |
| UI web do servidor histórico                    | Este alerta de nível de anfitrião é acionado se o UI web do servidor de história for inacessível.                                                                                                              |
| `ResourceManager`Web UI                   | Este alerta de nível de anfitrião é acionado se o `ResourceManager` UI web for inacessível.                                                                                                             |
| Resumo da Saúde NodeManager               | Este alerta de nível de serviço é desencadeado se houver NodeManagers pouco saudáveis                                                                                                                    |
| App Timeline Web UI                      | Este alerta de nível de anfitrião é desencadeado se o Web UI do Servidor de Timeline da aplicação estiver inacessível.                                                                                                         |
| Resumo da saúde dataNode                  | Este alerta de nível de serviço é desencadeado se houver DataNodes pouco saudáveis                                                                                                                       |
| NomeNode Web UI                          | Este alerta de nível de anfitrião é acionado se o II Web NameNode estiver inacessível.                                                                                                                    |
| Processo do controlador de falha do ZooKeeper    | Este alerta de nível de anfitrião é desencadeado se o processo do Controlador de Falha do ZooKeeper não puder ser confirmado para estar ligado e a ouvir na rede.                                                   |
| Oozie Server Web UI                      | Este alerta de nível de anfitrião é acionado se o UI Web do servidor Oozie não for acessível.                                                                                                                |
| Estado do servidor de Oozie                      | Este alerta de nível de anfitrião é desencadeado se o servidor Oozie não puder ser determinado a estar em pé e a responder aos pedidos do cliente.                                                                      |
| Processo de Metásta de Colmeia                   | Este alerta de nível de anfitrião é desencadeado se o processo de Metastessão de Colmeia não puder ser determinado a estar de pé e a ouvir na rede.                                                                 |
| Processo HiveServer2                      | Este alerta de nível de anfitrião é desencadeado se o HiveServer não puder ser determinado a estar de pé e responder aos pedidos do cliente.                                                                        |
| Estado do servidor WebHCat                    | Este alerta de nível de anfitrião é acionado se o estado do `templeton` servidor não for saudável.                                                                                                            |
| Por cento servidores zookeeper disponíveis      | Este alerta é desencadeado se o número de servidores do ZooKeeper no cluster for maior do que o limiar crítico configurado. Agrega os resultados das verificações do processo ZooKeeper.     |
| Servidor Spark2 Livy                       | Este alerta de nível de anfitrião é acionado se o Servidor Livy2 não puder ser determinado a levantar-se.                                                                                                        |
| Servidor histórico Spark2                    | Este alerta de nível de anfitrião é acionado se o Servidor histórico spark2 não puder ser determinado a estar em pé.                                                                                               |
| Processo de colecionador de métricas                | Este alerta é acionado se o Coletor de Métricas não puder ser confirmado para estar levantado e a ouvir na porta configurada por um número de segundos igual ao limiar.                                 |
| Colecionador de Métricas - HBase Master Process | Este alerta é acionado se os processos principais HBase do Colecionador de Métricas não puderem ser confirmados para estarem ligados e a ouvir na rede o limiar crítico configurado, dado em segundos. |
| Monitores por cento disponíveis       | Este alerta é desencadeado se uma percentagem dos processos do Metrics Monitor não estiver em cima e a ouvir na rede para os limiares de alerta e críticos configurados.                             |
| Por cento NodeManagers disponíveis           | Este alerta é desencadeado se o número de NodeManagers no cluster for maior do que o limiar crítico configurado. Agrega os resultados das verificações do processo NodeManager.        |
| NodeManager Health                       | Este alerta de nível de anfitrião verifica a propriedade de saúde do nó disponível a partir do componente NodeManager.                                                                                              |
| NodeManager Web UI                       | Este alerta de nível de anfitrião é acionado se o UI Web NodeManager não for acessível.                                                                                                                 |
| NomeNode Alta Disponibilidade Saúde        | Este alerta de nível de serviço é acionado se o Nome Activonode ou o Nome de StandbyNode não estiverem em execução.                                                                                     |
| Processo DataNode                         | Este alerta de nível de anfitrião é desencadeado se os processos dataNode individuais não puderem ser estabelecidos para estar em pé e ouvir na rede.                                                         |
| DataNode Web UI                          | Este alerta de nível de anfitrião é acionado se o UI Web DataNode estiver inacessível.                                                                                                                    |
| Por cento DiárioNodes disponíveis           | Este alerta é desencadeado se o número de Diárionodes no cluster for maior do que o limiar crítico configurado. Agrega os resultados das verificações do processo JournalNode.        |
| Por cento DataNodes Disponíveis              | Este alerta é desencadeado se o número de DataNodes no cluster for maior do que o limiar crítico configurado. Agrega os resultados das verificações do processo DataNode.              |
| Estado do servidor zeppelin                   | Este alerta de nível de anfitrião é desencadeado se o servidor Zeppelin não puder ser determinado a estar em pé e a responder aos pedidos do cliente.                                                                   |
| Processo Interativo HiveServer2          | Este alerta de nível de anfitrião é desencadeado se o HiveServerInteractive não puder ser determinado a estar em pé e a responder aos pedidos do cliente.                                                             |
| Aplicação LLAP                         | Este alerta é desencadeado se a Aplicação LLAP não puder ser determinada a levantar-se e responder aos pedidos.                                                                                    |

Pode selecionar cada serviço para ver mais informações sobre o mesmo.

Embora a página de serviço forneça informações sobre o estado e a configuração de cada serviço, não fornece informações sobre qual o nó de cabeça em que o serviço está em execução. Para ver esta informação, utilize o link **Hosts** no topo da página. Esta página apresenta anfitriões dentro do cluster, incluindo os nós da cabeça.

![Lista de anfitriões do cabeça de Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Selecionar o link para um dos nós da cabeça mostra os serviços e componentes em execução nesse nó.

![Estatuto de componente Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Para obter mais informações sobre a utilização de Ambari, consulte [o Monitor e gere o HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

A Ambari REST API está disponível através da internet. O gateway público HDInsight lida com pedidos de encaminhamento para o nó de cabeça que está atualmente a hospedar a API REST.

Pode utilizar o seguinte comando para verificar o estado de um serviço através da API Ambari REST:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Substitua **a palavra-passe** do utilizador HTTP (administrador).
* Substitua **CLUSTERNAME** pelo nome do cluster.
* Substitua o **SERVICENAME** pelo nome do serviço que pretende verificar o estado de.

Por exemplo, para verificar o estado do serviço **HDFS** num cluster chamado **mycluster**, com uma palavra-passe , utilizaria o seguinte comando: **password**

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

O Estado diz-nos que o serviço está atualmente em funcionamento, ou **INICIADO.**

Se não souber que serviços estão instalados no cluster, pode utilizar o seguinte comando para recuperar uma lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Para obter mais informações sobre o trabalho com a API Ambari REST, consulte [Monitor e Manage HDInsight utilizando a API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Componentes de serviço

Os serviços podem conter componentes que pretende verificar individualmente. Por exemplo, o HDFS contém o componente NameNode. Para ver informações sobre um componente, o comando seria:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Se não souber que componentes são fornecidos por um serviço, pode utilizar o seguinte comando para recuperar uma lista:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Como aceder a ficheiros de registo nos nos nosmes da cabeça

### <a name="ssh"></a>SSH

Enquanto ligados a um nó de cabeça através de SSH, os ficheiros de registo podem ser encontrados em **/var/log**. Por exemplo, **/var/log/hadoop-yarn/yarn** contêm registos para YARN.

Cada nó de cabeça pode ter entradas de registo únicas, por isso deve verificar os registos em ambos.

### <a name="sftp"></a>SFTP

Também pode ligar-se ao nó de cabeça utilizando o Protocolo de Transferência de Ficheiros SSH ou o Protocolo de Transferência de Ficheiros Seguro (SFTP) e fazer o download dos ficheiros de registo diretamente.

Semelhante à utilização de um cliente SSH, ao ligar-se ao cluster deve fornecer o nome da conta de utilizador SSH e o endereço SSH do cluster. Por exemplo, `sftp username@mycluster-ssh.azurehdinsight.net`. Forneça a palavra-passe para a conta quando solicitada, ou forneça uma chave pública utilizando o `-i` parâmetro.

Uma vez ligado, é-lhe apresentado um `sftp>` pedido. A partir desta solicitação, pode alterar diretórios, carregar e descarregar ficheiros. Por exemplo, os seguintes comandos mudam os diretórios para o diretório **/var/log/hadoop/hdfs** e, em seguida, descarregam todos os ficheiros no diretório.

```bash
cd /var/log/hadoop/hdfs
get *
```

Para obter uma lista de comandos disponíveis, `help` insira no `sftp>` aviso.

> [!NOTE]  
> Existem também interfaces gráficas que permitem visualizar o sistema de ficheiros quando conectado usando sFTP. Por exemplo, [o MobaXTerm](https://mobaxterm.mobatek.net/) permite-lhe navegar no sistema de ficheiros utilizando uma interface semelhante ao Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Para aceder aos ficheiros de registo usando ambari, tem de utilizar um túnel SSH. As interfaces web para cada serviço não são expostas publicamente na Internet. Para obter informações sobre a utilização de um túnel SSH, consulte o documento [de túnel SSH use.](hdinsight-linux-ambari-ssh-tunnel.md)

A partir do Ambari Web UI, selecione o serviço para o quais deseja visualizar registos (por exemplo, YARN). Em seguida, utilize **Links Rápidos** para selecionar qual nó de cabeça para visualizar os registos.

![Usando links rápidos para visualizar registos](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Como configurar o tamanho do nó

O tamanho de um nó só pode ser selecionado durante a criação do cluster. Pode encontrar uma lista dos diferentes tamanhos VM disponíveis para HDInsight na [página de preços hdInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Ao criar um cluster, pode especificar o tamanho dos nós. As seguintes informações fornecem orientações sobre como especificar o tamanho utilizando o [portal Azure](https://portal.azure.com/), [módulo Azure PowerShell Az](/powershell/azure/), e o [Azure CLI:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

* **Portal azul**: Ao criar um cluster, pode definir o tamanho dos nosmos utilizados pelo cluster:

    ![Imagem do assistente de criação de cluster com seleção de tamanho de nó](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: Ao utilizar o [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) comando, pode definir o tamanho dos nós da cabeça, do trabalhador e do ZooKeeper utilizando os `--headnode-size` , e os `--workernode-size` `--zookeepernode-size` parâmetros.

* **Azure PowerShell**: Ao utilizar o cmdlet [New-AzHDInsightCluster,](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) pode definir o tamanho da cabeça, do trabalhador e dos nós do ZooKeeper utilizando os , `-HeadNodeSize` e `-WorkerNodeSize` `-ZookeeperNodeSize` parâmetros.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Referência Apache Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalar e configurar o Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Instalar e configurar módulo Azure PowerShell Az](/powershell/azure/)
* [Gerir HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Provision Clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
