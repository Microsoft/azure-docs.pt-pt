---
title: Migrar clusters do Apache Hadoop no local para o Azure HDInsight - práticas recomendadas de infraestrutura
description: Aprenda as práticas recomendadas de infraestrutura para migrar clusters do Hadoop no local para Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 6c57b62d63be55abc51b85327957afffa5dd3a42
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360202"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrar clusters do Apache Hadoop no local para o Azure HDInsight - práticas recomendadas de infraestrutura

Este artigo fornece recomendações para a gestão da infraestrutura do Azure HDInsight clusters. É parte de uma série que fornece as práticas recomendadas para ajudar a migrar sistemas de Apache Hadoop no local para Azure HDInsight.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planejamento de capacidade de cluster do HDInsight

As opções principais para tornar para planeamento de capacidade de cluster do HDInsight são as seguintes:

- **Selecione a região** -a região do Azure determina onde o cluster fisicamente for aprovisionado. Para minimizar a latência de leituras e escritas, o cluster deve ser na mesma região que os dados.
- **Escolha a localização de armazenamento e o tamanho** -o armazenamento predefinido tem de estar na mesma região que o cluster. Para um cluster de nó de 48, recomenda-se de ter contas de armazenamento de 4 a 8. Embora já deve haver totais de armazenamento suficiente, cada conta de armazenamento fornece a largura de banda de rede adicional para os nós de computação. Quando existem várias contas de armazenamento, utilize um nome aleatório para cada conta de armazenamento, sem um prefixo. O objetivo de atribuição de nomes aleatórios é reduzir a possibilidade de afunilamentos de armazenamento (limitação) ou falhas de modo comum em todas as contas. Para um melhor desempenho, utilize apenas um contêiner por conta de armazenamento.
- **Escolha o tamanho da VM e o tipo (agora suporta a série G)** - cada tipo de cluster tem um conjunto de tipos de nó e cada tipo de nó tem opções específicas para seu tipo e tamanho VM. O tamanho da VM e o tipo é determinado pela CPU processamento, o tamanho de RAM e latência de rede. Uma carga de trabalho simulada pode ser utilizada para determinar o tamanho da VM ideal e o tipo para cada tipo de nó.
- **Escolha o número de nós de trabalho** -o número inicial de nós de trabalho pode ser determinado com as cargas de trabalho simuladas. O cluster pode ser dimensionado, mais tarde, ao adicionar mais nós de trabalho para os picos de procura de carga. O cluster mais tarde pode ser dimensionado quando os nós de trabalho adicionais não são necessários.

Para obter mais informações, consulte o artigo [planeamento de capacidade para clusters do HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Utilize o tipo de máquinas virtuais recomendados para o cluster

Ver [predefinido de tamanhos de máquina virtual e configuração de nó para clusters](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) para recomendado tipos de máquina virtual para cada tipo de cluster do HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Verificar a disponibilidade de componentes do Hadoop no HDInsight

Cada versão do HDInsight é uma distribuição de nuvem de uma versão do Hortonworks Data Platform (HDP) e consiste num conjunto de componentes do Hadoop sistema ao meio ambiente. Ver [controlo de versões de componente de HDInsight](../hdinsight-component-versioning.md) para obter detalhes sobre todos os componentes do HDInsight e suas versões atuais.

Também pode utilizar a IU do Apache Ambari ou a API de REST do Ambari para verificar as versões no HDInsight e componentes do Hadoop.

Aplicativos ou componentes que estavam disponíveis em clusters no local, mas não fazem parte dos clusters do HDInsight podem ser adicionados num nó de extremidade ou numa VM na mesma VNet que o cluster do HDInsight. Uma aplicação do Hadoop de terceiros que não está disponível no Azure HDInsight pode ser instalada utilizando a opção de "Aplicações" no cluster do HDInsight. Aplicações do Hadoop personalizadas podem ser instaladas num cluster do HDInsight com "ações de script". A tabela seguinte lista alguns dos aplicativos comuns e as opções de integração do HDInsight:

|**Aplicação**|**Integração**
|---|---|
|Ventilação|IaaS ou de extremidade de HDInsight nó
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Nenhum (apenas HDP)
|Datameer|HDInsight Edge node
|Datastax (Cassandra)|IaaS (CosmosDB alternativa no Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB alternativa no Azure)
|NiFi|IaaS 
|Presto|IaaS ou de extremidade de HDInsight nó
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQL dw alternativa no Azure)
|Tableau|IaaS 
|Waterline|HDInsight Edge node
|StreamSets|HDInsight Edge 
|Palantir|IaaS 
|Sailpoint|Iaas 

Para obter mais informações, consulte o artigo [Apache Hadoop componentes disponíveis com diferentes versões do HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Personalizar clusters do HDInsight com ações de script

HDInsight fornece um método de configuração de cluster chamado um **ação de script**. Uma ação de script é o script de Bash que é executado em nós num cluster do HDInsight e pode ser utilizado para instalar componentes adicionais e alterar as definições de configuração.

Ações de script devem ser armazenadas num URI que é acessível a partir do cluster do HDInsight. Eles podem ser utilizados durante ou após a criação do cluster e também podem ser restritos para executar apenas em determinados tipos de nó.

O script pode ser persistente ou executado uma vez. Os scripts persistentes são utilizados para personalizar novos nós de trabalho adicionados ao cluster através de operações de dimensionamento. Um script persistente também pode aplicar as alterações a outro tipo de nó, como um nó principal, quando ocorrem operações de dimensionamento.

HDInsight fornece pré-escritos scripts para instalar os seguintes componentes em clusters do HDInsight:

- Adicionar uma conta de Armazenamento do Azure
- Instalar o Hue
- Instalar prontamente
- Instalar Solr
- Instalar Giraph
- Pré-carregar as bibliotecas do Hive
- Instalar ou atualizar o Mono

> [!Note]  
> HDInsight não fornece suporte direto para componentes do hadoop personalizadas ou instalado utilizando as ações de script.

Ações de script também podem ser publicadas no Azure Marketplace como uma aplicação do HDInsight.

Para obter mais informações, veja os artigos seguintes:

- [Instalar aplicações do terceiro Apache Hadoop no HDInsight](../hdinsight-apps-install-applications.md)
- [Personalizar clusters do HDInsight com ações de script](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publicar uma aplicação de HDInsight no Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Personalizar as configurações do HDInsight com o arranque de

Por exemplo, é alterado para configurações nos ficheiros de configuração `core-site.xml`, `hive-site.xml` e `oozie-env.xml` podem ser realizadas utilizando o arranque. O script seguinte é um exemplo com o Powershell:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Para obter mais informações, consulte o artigo [clusters do HDInsight personalizar com o arranque de](../hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Nós de extremidade do cluster de ferramentas de cliente de acesso do HDInsight Hadoop

Um nó de extremidade vazio é uma máquina virtual do Linux com as mesmas ferramentas de cliente instalado e configurado como sobre os nós principais, mas com não existem serviços do Hadoop em execução. Nó de extremidade pode ser usado para os seguintes fins:

- aceder ao cluster
- teste de aplicativos de cliente
- alojamento de aplicações de cliente

Nós de extremidade podem ser criados e eliminado através do portal do Azure e podem ser utilizadas durante ou após a criação de cluster. Depois do nó de extremidade tiver sido criado, pode ligar ao nó de extremidade através de SSH e executar as ferramentas de cliente para aceder ao cluster de Hadoop no HDInsight. Nó de extremidade ssh ponto final é `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.


Para obter mais informações, consulte o artigo [utilizar nós de extremidade em branco em clusters do Apache Hadoop no HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Funcionalidade de ampliação e redução vertical de utilização de clusters

HDInsight fornece elasticidade, dando-lhe a opção de aumentar verticalmente e reduzir verticalmente o número de nós de trabalho nos seus clusters. Esta funcionalidade permite-lhe diminuir um cluster após horas ou no fim de semana e expanda-o durante picos de procura de negócios.

Dimensionamento do cluster pode ser automatizado através dos seguintes métodos:

### <a name="powershell-cmdlet"></a>Cmdlet do PowerShell

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

### <a name="azure-cli"></a>CLI do Azure

```powershell
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```

### <a name="azure-portal"></a>Portal do Azure

Ao adicionar nós ao cluster do HDInsight em execução, todas as tarefas em execução ou pendentes não serão afetadas. Novas tarefas podem ser submetidas com segurança enquanto o processo de dimensionamento está em execução. Se as operações de dimensionamento falhem por algum motivo, a falha é corretamente processada, mantendo o cluster num estado funcional.

No entanto, se reduzir verticalmente o cluster ao remover nós, todas as tarefas em execução ou pendentes falhará quando a operação de dimensionamento for concluída. Esse incumprimento seja causado por alguns dos serviços reiniciar durante o processo. Para resolver este problema, pode aguardar que as tarefas a concluir antes de dimensionar para baixo do seu cluster, manualmente terminar as tarefas ou volte a submeter as tarefas após tem concluíram a operação de dimensionamento.

Se reduzir o seu cluster para baixo para o mínimo de nó de um trabalho, HDFS poderá ficar bloqueado no modo de segurança, quando nós de trabalho são reiniciados para aplicação de patches ou imediatamente após a operação de dimensionamento. Pode executar o seguinte comando para HDFS sair do modo de segurança:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Depois de sair do modo de segurança, manualmente pode remover os ficheiros temporários, ou esperar para o Hive, eventualmente, limpá-los automaticamente.

Para obter mais informações, consulte o artigo [clusters do HDInsight de escala](../hdinsight-scaling-best-practices.md).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Utilizar o HDInsight com a rede Virtual do Azure

Redes virtuais do Azure permitem que os recursos do Azure, tais como máquinas virtuais do Azure, para comunicar entre si, da internet, de forma segura e redes no local, ao filtrar e encaminhar o tráfego de rede.

Utilizar Rede Virtual do Azure com o HDInsight permite que os seguintes cenários:

- Ligar ao HDInsight diretamente a partir de uma rede no local.
- A ligar o HDInsight aos dados armazena numa rede Virtual do Azure.
- Acedam diretamente aos serviços do Hadoop que não estão disponíveis publicamente na Internet. Por exemplo, as APIs de Kafka ou a API de Java do HBase.

HDInsight também pode ser adicionados a uma rede de Virtual de Azure nova ou existente. Se o HDInsight está a ser adicionado a uma rede Virtual existente, existente a grupos de segurança de rede e as rotas definidas pelo utilizador tem de ser atualizado para permitir o acesso sem restrições aos [vários endereços IP](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) no Centro de dados do Azure. Além disso, certifique-se de que não bloqueia o tráfego para o [portas](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ports) que estão a ser utilizada pelos serviços do HDInsight.

> [!Note]  
> HDInsight não suporta atualmente o túnel forçado. O túnel forçado é uma definição de sub-rede que força o tráfego de Internet de saída para um dispositivo para inspeção e registo. Remova o túnel forçado antes de instalar HDInsight numa sub-rede ou criar uma nova sub-rede para o HDInsight. HDInsight também não suporta a restringir a conectividade de rede de saída.

Para obter mais informações, veja os artigos seguintes:

- [Virtual-redes-descrição geral do Azure](../../virtual-network/virtual-networks-overview.md)
- [Expandir o Azure HDInsight utilizando uma rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Ligar de forma segura aos serviços do Azure com pontos finais de serviço de rede Virtual do Azure

HDInsight suporta [pontos finais de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) que permitem-lhe ligar de forma segura para as bases de dados do armazenamento de Blobs do Azure, geração 2 de armazenamento do Azure Data Lake, Cosmos DB e SQL. Ao ativar um ponto de extremidade de serviço para o Azure HDInsight, o tráfego flui através de uma rota segura no Centro de dados do Azure. Com este nível avançado de segurança na camada de rede, pode bloquear as contas de armazenamento de grandes volumes de dados às respetivas redes virtuais especificado (VNETs) e ainda usar facilmente clusters do HDInsight para aceder e processar dados.

Para obter mais informações, veja os artigos seguintes:

- [Pontos finais do serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Melhorar a segurança do HDInsight com pontos finais de serviço](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Ligar o HDInsight à rede no local

HDInsight pode ser ligadas à rede no local através de redes virtuais do Azure e um gateway de VPN. Os passos seguintes podem ser utilizados para estabelecer conectividade:

- Utilize o HDInsight numa rede Virtual do Azure que tenha conectividade à rede no local.
- Configure a resolução de nomes DNS entre a rede virtual e a rede no local.
- Configure grupos de segurança de rede ou rotas definidas pelo utilizador (UDR) para controlar o tráfego de rede.

Para obter mais informações, consulte o artigo [ligar o HDInsight à sua rede no local](../connect-on-premises-network.md)

## <a name="next-steps"></a>Passos Seguintes

Leia o artigo seguinte nesta série:

- [Melhores práticas do Storage para o local para a migração do Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md)
