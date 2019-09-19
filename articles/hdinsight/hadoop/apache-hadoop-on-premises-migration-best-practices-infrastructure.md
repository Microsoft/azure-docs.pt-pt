---
title: Migrar clusters de Apache Hadoop locais para o Azure HDInsight-infraestrutura
description: Aprenda práticas recomendadas de infraestrutura para migrar clusters Hadoop locais para o Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: e35917a7eb7b2e38fcc2689f6fe838ec6529428a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087456"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrar clusters de Apache Hadoop locais para o Azure HDInsight-práticas recomendadas de infraestrutura

Este artigo fornece recomendações para gerenciar a infraestrutura de clusters do Azure HDInsight. É parte de uma série que fornece as práticas recomendadas para ajudar na migração de sistemas locais Apache Hadoop para o Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planejar a capacidade do cluster HDInsight

As principais opções a serem feitas para o planejamento de capacidade do cluster HDInsight são as seguintes:

- **Escolha a região** – a região do Azure determina onde o cluster está fisicamente provisionado. Para minimizar a latência de leituras e gravações, o cluster deve estar na mesma região que os dados.
- **Escolha o local e o tamanho do armazenamento** -o armazenamento padrão deve estar na mesma região que o cluster. Para um cluster de 48 nós, é recomendável ter de 4 a 8 contas de armazenamento. Embora possa já haver um armazenamento total suficiente, cada conta de armazenamento fornece largura de banda de rede adicional para os nós de computação. Quando houver várias contas de armazenamento, use um nome aleatório para cada conta de armazenamento, sem um prefixo. A finalidade da nomenclatura aleatória é reduzir a chance de gargalos de armazenamento (limitação) ou falhas de modo comum em todas as contas. Para obter um melhor desempenho, use apenas um contêiner por conta de armazenamento.
- **Escolha o tamanho e o tipo da VM (agora dá suporte à série G)** -cada tipo de cluster tem um conjunto de tipos de nós e cada tipo de nó tem opções específicas para o tamanho e o tipo da VM. O tamanho e o tipo da VM são determinados pelo poder de processamento da CPU, pelo tamanho da RAM e pela latência de rede. Uma carga de trabalho simulada pode ser usada para determinar o tamanho ideal da VM e o tipo para cada um dos tipos de nó.
- **Escolha o número de nós de trabalho** -o número inicial de nós de trabalho pode ser determinado usando as cargas de trabalhos simuladas. O cluster pode ser dimensionado mais tarde, adicionando mais nós de trabalho para atender às demandas de carga de pico. Posteriormente, o cluster poderá ser dimensionado de volta quando os nós de trabalho adicionais não forem necessários.

Para obter mais informações, consulte o artigo [planejamento de capacidade para clusters HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Usar tipo de máquina virtual recomendado para cluster

Consulte [configuração de nó padrão e tamanhos de máquina virtual para clusters](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) para os tipos de máquina virtual recomendados para cada tipo de cluster HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Verificar a disponibilidade dos componentes do Hadoop no HDInsight

Cada versão do HDInsight é uma distribuição em nuvem de um conjunto de componentes do sistema de eco do Hadoop. Consulte [controle de versão de componente do hdinsight](../hdinsight-component-versioning.md) para obter detalhes sobre todos os componentes do hdinsight e suas versões atuais.

Você também pode usar a interface do usuário do Apache Ambari ou a API REST do Ambari para verificar os componentes e as versões do Hadoop no HDInsight.

Aplicativos ou componentes que estavam disponíveis em clusters locais, mas que não fazem parte dos clusters HDInsight, podem ser adicionados em um nó de borda ou em uma VM na mesma VNet que o cluster HDInsight. Um aplicativo Hadoop de terceiros que não está disponível no Azure HDInsight pode ser instalado usando a opção "aplicativos" no cluster HDInsight. Os aplicativos personalizados do Hadoop podem ser instalados no cluster HDInsight usando "ações de script". A tabela a seguir lista alguns dos aplicativos comuns e suas opções de integração do HDInsight:

|**Aplicação**|**Integrar**
|---|---|
|Ar|Nó de borda IaaS ou HDInsight
|Alluxio|IaaS  
|Exibição|IaaS 
|Atlas|Nenhum (somente HDP)
|Datameer|Nó de borda do HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB uma alternativa no Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB uma alternativa no Azure)
|NiFi|IaaS 
|Presto|Nó de borda IaaS ou HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|RÍGIDO|IaaS 
|Vertica|IaaS (SQLDW uma alternativa no Azure)
|Tableau|IaaS 
|Água|Nó de borda do HDInsight
|StreamSets|Borda do HDInsight 
|Palantir|IaaS 
|Sailpoint|IaaS 

Para obter mais informações, consulte o artigo [Apache Hadoop componentes disponíveis com diferentes versões do HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Personalizar clusters HDInsight usando ações de script

O HDInsight fornece um método de configuração de cluster chamado de **ação de script**. Uma ação de script é um script bash que é executado nos nós em um cluster HDInsight e pode ser usado para instalar componentes adicionais e alterar as definições de configuração.

As ações de script devem ser armazenadas em um URI que possa ser acessado pelo cluster HDInsight. Eles podem ser usados durante ou após a criação do cluster e também podem ser restritos para execução apenas em determinados tipos de nó.

O script pode ser persistido ou executado uma vez. Os scripts persistentes são usados para personalizar novos nós de trabalho adicionados ao cluster por meio de operações de dimensionamento. Um script persistente também pode aplicar alterações a outro tipo de nó, como um nó de cabeçalho, quando ocorrem operações de dimensionamento.

O HDInsight fornece scripts pré-gravados para instalar os seguintes componentes em clusters HDInsight:

- Adicionar uma conta de Armazenamento do Azure
- Instalar Hue
- Instalar prontamente
- Instalar Solr
- Instalar Giraph
- Pré-carregar bibliotecas do hive
- Instalar ou atualizar o Mono

> [!Note]  
> O HDInsight não fornece suporte direto para componentes ou componentes personalizados do Hadoop instalados usando ações de script.

As ações de script também podem ser publicadas no Azure Marketplace como um aplicativo do HDInsight.

Para obter mais informações, veja os artigos seguintes:

- [Instalar aplicativos de Apache Hadoop de terceiros no HDInsight](../hdinsight-apps-install-applications.md)
- [Personalizar clusters HDInsight usando ações de script](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publicar um aplicativo do HDInsight no Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Personalizar configurações do HDInsight usando a inicialização

Alterações nas configurações nos arquivos de configuração, como, `core-site.xml` `hive-site.xml` e `oozie-env.xml` podem ser feitas usando a inicialização. O script a seguir é um exemplo que usa o cmdlet [AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) do PowerShell [New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster):

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

Para obter mais informações, consulte o artigo [Personalizar clusters HDInsight usando a inicialização](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Consulte também [gerenciar clusters HDInsight usando a API REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Acessar as ferramentas de cliente dos nós de borda do cluster Hadoop do HDInsight

Um nó de borda vazio é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos nós de cabeçalho, mas sem serviços do Hadoop em execução. O nó de borda pode ser usado para as seguintes finalidades:

- Acessando o cluster
- Testando aplicativos cliente
- hospedando aplicativos cliente

Os nós de borda podem ser criados e excluídos por meio do portal do Azure e podem ser usados durante ou após a criação do cluster. Depois que o nó de borda tiver sido criado, você poderá se conectar ao nó de borda usando SSH e executar ferramentas de cliente para acessar o cluster Hadoop no HDInsight. O ponto de extremidade SSH do `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`nó de borda é.


Para obter mais informações, consulte o artigo [usar nós de borda vazios em clusters de Apache Hadoop no HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Usar o recurso de expansão e redução vertical dos clusters

O HDInsight fornece elasticidade fornecendo a opção de escalar verticalmente e reduzir o número de nós de trabalho em seus clusters. Esse recurso permite que você reduza um cluster após horas ou nos finais de semana e expanda-o durante as demandas de negócios de pico. Para obter mais informações, consulte:

* [Dimensionar clusters HDInsight](../hdinsight-scaling-best-practices.md).
* [Dimensionar clusters](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Usar o HDInsight com a rede virtual do Azure

As redes virtuais do Azure permitem que os recursos do Azure, como máquinas virtuais do Azure, comuniquem-se com segurança entre si, Internet e redes locais, filtrando e roteando tráfego de rede.

Usar a rede virtual do Azure com o HDInsight habilita os seguintes cenários:

- Conectar-se ao HDInsight diretamente de uma rede local.
- Conectando o HDInsight a armazenamentos de dados em uma rede virtual do Azure.
- Acessando diretamente os serviços do Hadoop que não estão disponíveis publicamente pela Internet. Por exemplo, APIs Kafka ou a API Java do HBase.

O HDInsight pode ser adicionado a uma rede virtual do Azure nova ou existente. Se o HDInsight estiver sendo adicionado a uma rede virtual existente, os grupos de segurança de rede e as rotas definidas pelo usuário existentes precisarão ser atualizados para permitir acesso irrestrito a [vários endereços IP](../hdinsight-management-ip-addresses.md) na data center do Azure. Além disso, certifique-se de não bloquear o tráfego para as [portas](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports), que estão sendo usadas pelos serviços do HDInsight.

> [!Note]  
> No momento, o HDInsight não dá suporte ao túnel forçado. O túnel forçado é uma configuração de sub-rede que força o tráfego de Internet de saída para um dispositivo para inspeção e registro em log. Remova o túnel forçado antes de instalar o HDInsight em uma sub-rede ou crie uma nova sub-rede para o HDInsight. O HDInsight também não dá suporte à restrição de conectividade de rede de saída.

Para obter mais informações, veja os artigos seguintes:

- [Redes virtuais do Azure-visão geral](../../virtual-network/virtual-networks-overview.md)
- [Expandir o Azure HDInsight utilizando uma rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Conectar-se com segurança aos serviços do Azure com pontos de extremidade de serviço de rede virtual do Azure

O HDInsight dá suporte a [pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md), que permitem que você se conecte com segurança ao armazenamento de BLOBs do Azure, Azure Data Lake Storage Gen2, Cosmos DB e bancos de dados SQL. Habilitando um ponto de extremidade de serviço para o Azure HDInsight, o tráfego flui através de uma rota segura de dentro do data center do Azure. Com esse nível aprimorado de segurança na camada de rede, você pode bloquear Big Data contas de armazenamento para suas redes virtuais especificadas (VNETs) e ainda usar clusters HDInsight diretamente para acessar e processar esses dados.

Para obter mais informações, veja os artigos seguintes:

- [Pontos finais de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Aprimore a segurança do HDInsight com pontos de extremidade de serviço](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Conectar o HDInsight à rede local

O HDInsight pode ser conectado à rede local usando redes virtuais do Azure e um gateway de VPN. As etapas a seguir podem ser usadas para estabelecer a conectividade:

- Use o HDInsight em uma rede virtual do Azure que tenha conectividade com a rede local.
- Configure a resolução de nomes DNS entre a rede virtual e a rede local.
- Configure grupos de segurança de rede ou UDR (rotas definidas pelo usuário) para controlar o tráfego de rede.

Para obter mais informações, consulte o artigo [conectar o HDInsight à sua rede local](../connect-on-premises-network.md)

## <a name="next-steps"></a>Passos Seguintes

Leia o próximo artigo desta série:

- [Práticas recomendadas de armazenamento para migração local para Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md)
