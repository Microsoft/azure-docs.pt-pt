---
title: 'Infraestrutura: No local Apache Hadoop para Azure HDInsight'
description: Aprenda as melhores práticas de infraestrutura para migrar no local aglomerados Hadoop para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: d7ee8ae121e3cbb9760a87c95d12109a9b05e0c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951518"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrar no local Apache Hadoop clusters para Azure HDInsight - infraestruturas melhores práticas

Este artigo dá recomendações para a gestão da infraestrutura dos clusters Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar na migração dos sistemas Apache Hadoop para o Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Plano para a capacidade de cluster do HDInsight

As escolhas-chave a fazer para o planeamento da capacidade de cluster HDInsight são as seguintes:

**Região**  
A região de Azure determina onde o aglomerado é fisicamente provisionado. Para minimizar a latência das leituras e dos escritos, o cluster deve estar na mesma Região que os dados.

**Localização e tamanho de armazenamento**  
O armazenamento predefinido deve estar na mesma região que o cluster.Para um cluster de 48 nós, recomenda-se ter 4 a 8 contas de armazenamento. Embora já possa haver armazenamento total suficiente, cada conta de armazenamento fornece largura de banda de rede adicional para os nós de computação. Quando existirem várias contas de armazenamento, utilize um nome aleatório para cada conta de armazenamento, sem prefixo. O objetivo da nomeação aleatória é reduzir a possibilidade de estrangulamentos de armazenamento (estrangulamento) ou falhas em modo comum em todas as contas. Para um melhor desempenho, utilize apenas um recipiente por conta de armazenamento.

**Tamanho e tipo VM (agora suporta a série G)**  
Cada tipo de cluster tem um conjunto de tipos de nó, e cada tipo de nó tem opções específicas para o seu tamanho e tipo vm. O tamanho e o tipo vm são determinados pela potência de processamento do CPU, tamanho RAM e latência da rede. Uma carga de trabalho simulada pode ser usada para determinar o tamanho e o tipo de VM ideais para cada tipo de nó.

**Número de nós operários**  
O número inicial de nós de trabalhador pode ser determinado utilizando as cargas de trabalho simuladas. O cluster pode ser escalado mais tarde, adicionando mais nós de trabalhadores para satisfazer as exigências de carga máxima. O cluster pode ser posteriormente reduzido quando os nós de trabalhadores adicionais não são necessários.

Para mais informações, consulte o artigo Planeamento de [Capacidade para clusters HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Utilize o tipo de máquina virtual recomendado para o cluster

Consulte a [configuração do nó Predefinido e os tamanhos das máquinas virtuais para os clusters](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) para os tipos de máquinas virtuais recomendados para cada tipo de cluster HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Verifique a disponibilidade de componentes Hadoop no HDInsight

Cada versão HDInsight é uma distribuição em nuvem de um conjunto de componentes do ecossistema Hadoop. Consulte a [versão do componente HDInsight](../hdinsight-component-versioning.md) para obter detalhes sobre todos os componentes HDInsight e as suas versões atuais.

Também pode utilizar a Apache Ambari UI ou ambari REST API para verificar os componentes e versões Hadoop no HDInsight.

Aplicações ou componentes que estavam disponíveis em clusters no local, mas que não fazem parte dos clusters HDInsight, podem ser adicionados num nó de borda ou num VM no mesmo VNet que o cluster HDInsight. Uma aplicação Hadoop de terceiros que não esteja disponível no Azure HDInsight pode ser instalada usando a opção "Aplicações" no cluster HDInsight. As aplicações Personalizadas hadoop podem ser instaladas no cluster HDInsight utilizando "ações de script". A tabela seguinte lista algumas das aplicações comuns e as suas opções de integração HDInsight:

|**Aplicação**|**Integração**
|---|---|
|Fluxo de ar|Nó de borda IaaS ou HDInsight
|Aludilo|IaaS  
|Arcádia|IaaS 
|Atlas|Nenhum (apenas HDP)
|Datameer|Nó de borda HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB uma alternativa em Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Inflamar|IaaS
|Rio Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB uma alternativa em Azure)
|NiFi|IaaS 
|Presto|Nó de borda IaaS ou HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW uma alternativa em Azure)
|Tableau|IaaS 
|Linha de água|Nó de borda HDInsight
|Conjuntos de streaming|Borda HDInsight 
|Palantir|IaaS 
|Ponta-vela|Iaas 

Para mais informações, consulte o artigo [Componentes Apache Hadoop disponíveis com diferentes versões HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Personalize os clusters HDInsight usando ações de script

HDInsight fornece um método de configuração de cluster chamado **ação de script**. Uma ação de script é o script Bash que corre nos nós de um cluster HDInsight e pode ser usado para instalar componentes adicionais e alterar configurações de configuração.

As ações do script devem ser armazenadas num URI que seja acessível a partir do cluster HDInsight. Podem ser utilizados durante ou após a criação do cluster e também podem ser restringidos a funcionar apenas em certos tipos de nós.

O guião pode ser percedido ou executado uma vez. Os scripts persistidos são usados para personalizar novos nós de trabalhador adicionados ao cluster através de operações de escala. Um script persistente também pode aplicar alterações a outro tipo de nó, como um nó de cabeça, quando ocorrem operações de escala.

O HDInsight fornece scripts pré-escritos para instalar os seguintes componentes em clusters HDInsight:

- Adicione uma conta de Armazenamento Azure
- Instalar Matiz
- Instalar Presto
- Instalar Solr
- Instalar giraph
- Bibliotecas colmeias pré-carregadas
- Instalar ou atualizar o Mono

> [!Note]  
> O HDInsight não fornece suporte direto para componentes ou componentes de hadoop personalizados instalados utilizando ações de script.

As ações do script também podem ser publicadas no Azure Marketplace como uma aplicação HDInsight.

Para obter mais informações, veja os artigos seguintes:

- [Instale aplicações apache hadoop de terceiros no HDInsight](../hdinsight-apps-install-applications.md)
- [Personalize os clusters HDInsight usando ações de script](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publique uma aplicação HDInsight no Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Personalize os configs HDInsight usando a Bootstrap

Alterações aos configs nos ficheiros `hive-site.xml` `oozie-env.xml` de config, tais como `core-site.xml`, e podem ser feitas usando bootstrap. O seguinte script é um exemplo utilizando o módulo Powershell [AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) cmdlet [New-AzHDInsightClusterConfig:](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)

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

Para mais informações, consulte o artigo Customize os [clusters HDInsight usando bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Consulte também, [Gerencie os clusters HDInsight utilizando a API De REPOUSO Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Aceda às ferramentas do cliente a partir de nós de borda de cluster HDInsight Hadoop

Um nó de borda vazia é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos nós da cabeça, mas sem serviços Hadoop em funcionamento. O nó de borda pode ser utilizado para os seguintes fins:

- acesso ao cluster
- testar aplicações de clientes
- hospedagem de aplicações de clientes

Os nós de borda podem ser criados e eliminados através do portal Azure e podem ser usados durante ou após a criação do cluster. Depois de criado o nó de borda, pode ligar-se ao nó de borda utilizando SSH e executar ferramentas de cliente para aceder ao cluster Hadoop no HDInsight. O ponto final do nó `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`de borda é .


Para mais informações, consulte o artigo Utilize nós de [borda vazia em clusters Apache Hadoop em HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Utilize a função de escalada e escala-down de clusters

O HDInsight proporciona elasticidade, dando-lhe a opção de escalar e reduzir o número de nós dos trabalhadores nos seus clusters. Esta funcionalidade permite-lhe encolher um cluster fora de horas ou aos fins de semana e expandi-lo durante as exigências de negócios. Para obter mais informações, consulte:

* [Clusters De Escala HDInsight](../hdinsight-scaling-best-practices.md).
* [Aglomerados de escala.](../hdinsight-administer-use-portal-linux.md#scale-clusters)

## <a name="use-hdinsight-with-azure-virtual-network"></a>Utilizar o HDInsight com a Rede Virtual Azure

As Redes Virtuais Azure permitem que os recursos do Azure, como as Máquinas Virtuais Azure, se comuniquem de forma segura entre si, as redes de internet e no local, filtrando e encaminhando o tráfego da rede.

Utilizar a Rede Virtual Azure com o HDInsight permite os seguintes cenários:

- Conectando-se ao HDInsight diretamente de uma rede no local.
- Ligar o HDInsight a lojas de dados numa rede Azure Virtual.
- Acedendo diretamente aos serviços Hadoop que não estão disponíveis publicamente através da internet. Por exemplo, Kafka APIs ou a HBase Java API.

O HDInsight pode ser adicionado a uma rede virtual Azure nova ou existente. Se o HDInsight estiver a ser adicionado a uma Rede Virtual existente, os grupos de segurança de rede existentes e as rotas definidas pelo utilizador precisam de ser atualizadas para permitir o acesso sem restrições a [vários endereços IP](../hdinsight-management-ip-addresses.md) no centro de dados Azure. Além disso, certifique-se de não bloquear o tráfego para as [portas](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports), que estão a ser utilizadas pelos serviços HDInsight.

> [!Note]  
> O HDInsight não suporta atualmente túneis forçados. A escavação forçada é uma subrede que obriga a saída do tráfego da Internet para um dispositivo de inspeção e exploração madeireira. Ou remova o túnel forçado antes de instalar o HDInsight numa subneta ou crie uma nova sub-rede para o HDInsight. O HDInsight também não suporta restringir a conectividade da rede de saída.

Para obter mais informações, veja os artigos seguintes:

- [Visão geral de redes virtuais azure](../../virtual-network/virtual-networks-overview.md)
- [Expandir o Azure HDInsight com uma Rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Ligue-se de forma segura aos serviços Azure com pontos finais de serviço da Rede Virtual Azure

O HDInsight suporta [pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md)de serviço de rede virtual, que permitem ligar-se de forma segura às bases de armazenamento de Blob Azure, Azure Data Lake Storage Gen2, Cosmos DB e SQL. Ao ativar um ponto final de serviço para o Azure HDInsight, o tráfego flui através de uma rota segura a partir do centro de dados Azure. Com este nível de segurança melhorado na camada de rede, pode bloquear grandes contas de armazenamento de dados às suas redes virtuais especificadas (VNETs) e ainda utilizar os clusters HDInsight sem problemas para aceder e processar esses dados.

Para obter mais informações, veja os artigos seguintes:

- [Pontos finais de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Melhorar a segurança hDInsight com pontos finais do serviço](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Ligue o HDInsight à rede no local

O HDInsight pode ser ligado à rede no local utilizando redes virtuais Azure e um gateway VPN. Os seguintes passos podem ser utilizados para estabelecer a conectividade:

- Utilize o HDInsight numa Rede Virtual Azure que tenha conectividade com a rede no local.
- Configure a resolução de nomes DNS entre a rede virtual e a rede no local.
- Configure grupos de segurança de rede ou rotas definidas pelo utilizador (UDR) para controlar o tráfego da rede.

Para mais informações, consulte o artigo [Connect HDInsight à sua rede no local](../connect-on-premises-network.md)

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série: Armazenamento das [melhores práticas para a migração de Hadoop Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-storage.md).
