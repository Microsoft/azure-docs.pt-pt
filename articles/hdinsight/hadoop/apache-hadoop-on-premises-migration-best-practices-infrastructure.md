---
title: 'Infraestrutura: No local Apache Hadoop a Azure HDInsight'
description: Aprenda as melhores práticas de infraestrutura para migrar nos locais dos clusters Hadoop para Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 309053c2d7a0f9482016f1bd83e0c61dcd31bec5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740669"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrar para as instalações apache Hadoop clusters para Azure HDInsight - melhores práticas de infraestrutura

Este artigo dá recomendações para a gestão da infraestrutura de clusters Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas Apache Hadoop para Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Plano para a capacidade de cluster do HDInsight

As principais escolhas a fazer para o planeamento da capacidade do cluster HDInsight são as seguintes:

**Região**  
A região de Azure determina onde o aglomerado é fisicamente a provisionado. Para minimizar a latência das leituras e das gravações, o cluster deve estar na mesma Região que os dados.

**Local de armazenamento e tamanho**  
O armazenamento predefinido deve estar na mesma região que o aglomerado. Para um cluster de 48 nós, recomenda-se ter 4 a 8 contas de armazenamento. Embora já possa haver armazenamento total suficiente, cada conta de armazenamento fornece largura de banda adicional para os nós de computação. Quando houver várias contas de armazenamento, utilize um nome aleatório para cada conta de armazenamento, sem prefixo. O objetivo do naming aleatório é reduzir a possibilidade de estrangulamentos de armazenamento (estrangulamento) ou falhas de modo comum em todas as contas. Para um melhor desempenho, utilize apenas um recipiente por conta de armazenamento.

**Tamanho e tipo VM (agora suporta a série G)**  
Cada tipo de cluster tem um conjunto de tipos de nós, e cada tipo de nó tem opções específicas para o seu tamanho e tipo VM. O tamanho e tipo VM é determinado pelo poder de processamento da CPU, tamanho RAM e latência da rede. Uma carga de trabalho simulada pode ser usada para determinar o tamanho e o tipo de VM ideais para cada tipo de nó.

**Número de nós de trabalhadores**  
O número inicial de nós de trabalhadores pode ser determinado utilizando as cargas de trabalho simuladas. O cluster pode ser escalado mais tarde adicionando mais nós operários para satisfazer as exigências de carga máxima. O cluster pode ser mais tarde reduzido quando os nós de trabalho adicionais não são necessários.

Para mais informações, consulte o artigo [Planeamento de capacidade para clusters HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Utilize o tipo de máquina virtual recomendado para o cluster

Consulte [a configuração do nó predefinido e os tamanhos de máquinas virtuais para os conjuntos](../hdinsight-supported-node-configuration.md) de máquinas virtuais recomendadas para cada tipo de cluster HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Verifique a disponibilidade de componentes hadoop em HDInsight

Cada versão HDInsight é uma distribuição em nuvem de um conjunto de componentes do ecossistema Hadoop. Consulte [a versão do componente HDInsight](../hdinsight-component-versioning.md) para obter detalhes sobre todos os componentes HDInsight e as suas versões atuais.

Também pode utilizar a Apache Ambari UI ou a Ambari REST API para verificar os componentes e versões Hadoop em HDInsight.

Aplicações ou componentes que estavam disponíveis em clusters no local, mas que não fazem parte dos clusters HDInsight, podem ser adicionados num nó de borda ou num VM no mesmo VNet que o cluster HDInsight. Uma aplicação Hadoop de terceiros que não esteja disponível no Azure HDInsight pode ser instalada usando a opção "Aplicações" no cluster HDInsight. As aplicações Custom Hadoop podem ser instaladas no cluster HDInsight utilizando "ações de script". A tabela a seguir enumera algumas das aplicações comuns e as suas opções de integração HDInsight:

|**Aplicação**|**Integração**
|---|---|
|Fluxo de ar|Nó de borda iaaS ou HDInsight
|Rio Alluxio|IaaS  
|Arcádia|IaaS 
|Atlas|Nenhum (apenas HDP)
|Datameer|Nó de borda HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB uma alternativa em Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignição|IaaS
|Rio Jethro|IaaS 
|Mapador|IaaS 
|Rio Mongo|IaaS (CosmosDB uma alternativa em Azure)
|NiFi|IaaS 
|Presto|Nó de borda iaaS ou HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW uma alternativa em Azure)
|Tableau|IaaS 
|Linha de água|Nó de borda HDInsight
|StreamSets|Borda HDInsight 
|Palantir|IaaS 
|Ponto de navegação|Iaas 

Para mais informações, consulte o artigo [Componentes Apache Hadoop disponíveis com diferentes versões HDInsight](../hdinsight-component-versioning.md)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Personalize clusters HDInsight usando ações de script

O HDInsight fornece um método de configuração de cluster chamado **ação de script**. Uma ação de script é o script Bash que executa os nós num cluster HDInsight e pode ser usado para instalar componentes adicionais e alterar definições de configuração.

As ações do script devem ser armazenadas num URI acessível a partir do cluster HDInsight. Podem ser utilizados durante ou após a criação do cluster e também podem ser restringidos a funcionar apenas em certos tipos de nós.

O guião pode ser persistido ou executado uma vez. Os scripts persistidos são usados para personalizar novos nós de trabalhador adicionados ao cluster através de operações de escala. Um script persistido também pode aplicar alterações a outro tipo de nó, como um nó de cabeça, quando ocorrem operações de escalonamento.

O HDInsight fornece scripts pré-escritos para instalar os seguintes componentes em clusters HDInsight:

- Adicione uma conta de Armazenamento Azure
- Instalar Matiz
- Instalar Presto
- Instalar Solr
- Instalar Giraph
- Pré-carregar bibliotecas de Colmeias
- Instalar ou atualizar o Mono

> [!Note]  
> O HDInsight não fornece suporte direto para componentes ou componentes de hadoop personalizados instalados usando ações de script.

As ações de script também podem ser publicadas no Azure Marketplace como uma aplicação HDInsight.

Para obter mais informações, veja os seguintes artigos:

- [Instalar aplicações Apache Hadoop de terceiros em HDInsight](../hdinsight-apps-install-applications.md)
- [Personalize clusters HDInsight usando ações de script](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publicar uma aplicação HDInsight no Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Personalize configurações HDInsight usando Bootstrap

Alterações nas configurações nos ficheiros config tais como `core-site.xml` , e podem ser `hive-site.xml` `oozie-env.xml` feitas usando Bootstrap. O seguinte script é um exemplo usando o módulo PowerShell [AZ](/powershell/azure/new-azureps-module-az) cmdlet [New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightcluster):

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

Para obter mais informações, consulte o artigo [Personalize os clusters HDInsight utilizando o Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Consulte também os [clusters HDInsight utilizando o Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Aceda às ferramentas do cliente a partir de nódos de borda de cluster hdInsight Hadoop

Um nó de borda vazia é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos nós da cabeça, mas sem serviços Hadoop em funcionamento. O nó de borda pode ser utilizado para os seguintes fins:

- acesso ao cluster
- testar aplicações de clientes
- hospedar aplicações de clientes

Os nós de borda podem ser criados e eliminados através do portal Azure e podem ser usados durante ou após a criação do cluster. Depois de criado o nó de borda, pode ligar-se ao nó de borda utilizando SSH e executar ferramentas de cliente para aceder ao cluster Hadoop em HDInsight. O ponto final do nó de borda ssh é `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22` .


Para obter mais informações, consulte o artigo [Utilize nós de borda vazia nos aglomerados Apache Hadoop em HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Utilize a característica de escala e de escala dos clusters

O HDInsight proporciona elasticidade, dando-lhe a opção de escalar e reduzir o número de nós de trabalhadores nos seus clusters. Esta funcionalidade permite-lhe encolher um cluster fora de horas ou aos fins de semana e expandi-lo durante as exigências máximas do negócio. Para obter mais informações, consulte:

* [Aglomerados HDInsight escala](../hdinsight-scaling-best-practices.md).
* [Aglomerados de escala](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Use HDInsight com Rede Virtual Azure

As Redes Virtuais Azure permitem que os recursos do Azure, como as Máquinas Virtuais Azure, comuniquem de forma segura entre si, a internet e as redes no local, filtrando e encaminhando o tráfego da rede.

A utilização da Rede Virtual Azure com o HDInsight permite os seguintes cenários:

- Ligação ao HDInsight diretamente a partir de uma rede no local.
- Ligação hdInsight às lojas de dados numa rede Virtual Azure.
- Acedendo diretamente aos serviços hadoop que não estão disponíveis publicamente através da internet. Por exemplo, APIs Kafka ou a HBase Java API.

O HDInsight pode ser adicionado a uma nova rede virtual Azure ou existente. Se o HDInsight estiver a ser adicionado a uma Rede Virtual existente, os grupos de segurança de rede existentes e as rotas definidas pelo utilizador devem ser atualizados para permitir o acesso ilimitado a [vários endereços IP](../hdinsight-management-ip-addresses.md) no centro de dados Azure. Além disso, certifique-se de não bloquear o tráfego para as portas , que estão sendo [usadas](../control-network-traffic.md#required-ports)pelos serviços HDInsight.

> [!Note]  
> HdInsight não suporta atualmente a escavação forçada. O túnel forçado é uma definição de sub-rede que força o tráfego de saída da Internet a um dispositivo de inspeção e registo. Remova o túnel forçado antes de instalar o HDInsight numa sub-rede ou crie uma nova sub-rede para o HDInsight. O HDInsight também não suporta restringir a conectividade da rede de saída.

Para obter mais informações, veja os seguintes artigos:

- [Visão geral das redes virtuais do Azure](../../virtual-network/virtual-networks-overview.md)
- [Expandir o Azure HDInsight com uma Rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Ligar-se de forma segura aos serviços da Azure com os pontos finais do serviço Azure Virtual Network

O HDInsight suporta [pontos finais de serviço de rede virtuais,](../../virtual-network/virtual-network-service-endpoints-overview.md)que lhe permitem ligar-se de forma segura ao Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB e bases de dados SQL. Ao permitir um Ponto de Serviço para Azure HDInsight, o tráfego flui através de uma rota segura a partir de dentro do centro de dados Azure. Com este nível de segurança melhorado na camada de rede, pode bloquear as grandes contas de armazenamento de dados nas suas redes virtuais especificadas (VNETs) e ainda utilizar clusters HDInsight de forma perfeita para aceder e processar esses dados.

Para obter mais informações, veja os seguintes artigos:

- [Pontos finais de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Aumentar a segurança HDInsight com pontos finais de serviço](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Ligue o HDInsight à rede no local

O HDInsight pode ser ligado à rede no local utilizando redes virtuais Azure e um gateway VPN. Podem ser utilizados os seguintes passos para estabelecer conectividade:

- Utilize o HDInsight numa Rede Virtual Azure que tenha conectividade com a rede no local.
- Configure a resolução de nome DNS entre a rede virtual e a rede no local.
- Configure grupos de segurança de rede ou rotas definidas pelo utilizador (UDR) para controlar o tráfego da rede.

Para mais informações, consulte o artigo [Connect HDInsight para a sua rede no local](../connect-on-premises-network.md)

## <a name="next-steps"></a>Passos seguintes

Leia o artigo seguinte nesta série: [Armazenamento das melhores práticas para a migração de Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md).