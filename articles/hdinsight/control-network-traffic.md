---
title: Tráfego de rede de controlo em Azure HDInsight
description: Aprenda técnicas para controlar o tráfego de entrada e saída para clusters Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 66b078e8dfa1daa100978f04283e9bba7158bddf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867037"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Tráfego de rede de controlo em Azure HDInsight

O tráfego de rede numa Rede Virtual Azure pode ser controlado utilizando os seguintes métodos:

* **Os grupos de segurança da** rede (NSG) permitem filtrar o tráfego de entrada e saída para a rede. Para obter mais informações, consulte o tráfego da rede Filter com o documento [dos grupos de segurança da rede.](../virtual-network/network-security-groups-overview.md)

* **Os aparelhos virtuais de rede** (NVA) só podem ser utilizados com tráfego de saída. Os NVAs replicam a funcionalidade de dispositivos como firewalls e routers. Para obter mais informações, consulte o documento ["Aparelhos de Rede".](https://azure.microsoft.com/solutions/network-appliances)

Como um serviço gerido, o HDInsight requer acesso ilimitado aos serviços de saúde e gestão HDInsight, tanto para o tráfego de entrada e saída do VNET. Ao utilizar NSGs, deve garantir que estes serviços ainda podem comunicar com o cluster HDInsight.

:::image type="content" source="./media/control-network-traffic/hdinsight-vnet-diagram.png" alt-text="Diagrama de entidades HDInsight criadas em Azure custom VNET" border="false":::

## <a name="hdinsight-with-network-security-groups"></a>HDInsight com grupos de segurança de rede

Se planeia utilizar **grupos de segurança** de rede para controlar o tráfego da rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifique a região de Azure que pretende utilizar para o HDInsight.

2. Identifique as etiquetas de serviço exigidas pela HDInsight para a sua região. Existem várias formas de obter estas etiquetas de serviço:
    1. Consulte a lista de tags de serviço publicadas em tags de [serviço do Grupo de Segurança da Rede (NSG) para Azure HDInsight](hdinsight-service-tags.md). 
    2. Se a sua região não estiver presente na lista, utilize a [API de Deteção de Etiquetas](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) de Serviço para encontrar uma etiqueta de serviço para a sua região.
    3. Se não conseguir utilizar a API, descarregue o [ficheiro JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) da marca de serviço e procure a região desejada.


3. Crie ou modifique os grupos de segurança da rede para a sub-rede em que planeia instalar o HDInsight.

    * __Grupos de segurança da__ rede : permitir o tráfego __de entrada na__ porta __443__ a partir dos endereços IP. Isto garantirá que os serviços de gestão HDInsight possam chegar ao cluster de fora da rede virtual. Para clusters de __procuração de Kafka REST,__ permita o tráfego __de entrada no__ porto __9400__ também. Isto garantirá que o servidor proxy Kafka REST está acessível.

Para obter mais informações sobre grupos de segurança de rede, consulte a [visão geral dos grupos de segurança da rede.](../virtual-network/network-security-groups-overview.md)

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controlo do tráfego de saída dos clusters HDInsight

Para obter mais informações sobre o controlo do tráfego de saída dos clusters HDInsight, consulte [a restrição de tráfego de saída Configure para os clusters Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Túneis forçados para o local

O túnel forçado é uma configuração de encaminhamento definida pelo utilizador onde todo o tráfego de uma sub-rede é forçado a uma rede ou localização específica, como a sua rede no local ou firewall. Não _é_ recomendada a transferência forçada de todos os dados para o local devido a grandes volumes de transferência de dados e ao impacto potencial do desempenho.

Os clientes interessados em configurar túneis forçados, devem utilizar [as metastores personalizadas](./hdinsight-use-external-metadata-stores.md) e configurar a conectividade adequada da sub-rede de cluster ou da rede no local para estas metastões personalizadas.

Para ver um exemplo da configuração da UDR com o Azure Firewall, consulte [a restrição de tráfego de saída Configure para os clusters Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ports"></a>Portos necessários

Se planeia usar uma **firewall** e aceder ao cluster a partir de fora em determinadas portas, poderá ter de permitir o tráfego nas portas necessárias para o seu cenário. Por predefinição, não é necessária uma filtragem especial das portas desde que o tráfego de gestão Azure explicado na secção anterior possa chegar ao cluster na porta 443.

Para obter uma lista de portas para serviços específicos, consulte os [Ports utilizados pelos serviços Apache Hadoop no documento HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

Para obter mais informações sobre as regras de firewall para aparelhos virtuais, consulte o documento [de cenário do aparelho virtual.](../virtual-network/virtual-network-scenario-udr-gw-nva.md)

## <a name="next-steps"></a>Passos seguintes

* Para obter amostras de código e exemplos de criação de Redes Virtuais Azure, consulte [criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* Para obter um exemplo de configuração hdinsight para ligar a uma rede no local, consulte [Connect HDInsight a uma rede no local](./connect-on-premises-network.md).
* Para obter mais informações sobre as redes virtuais Azure, consulte a [visão geral da Rede Virtual Azure](../virtual-network/virtual-networks-overview.md).
* Para obter mais informações sobre grupos de segurança de rede, consulte [os grupos de segurança da rede](../virtual-network/network-security-groups-overview.md).
* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [as rotas definidas pelo Utilizador e o encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).
* Para obter mais informações sobre redes virtuais, consulte [Plano VNETs para HDInsight](./hdinsight-plan-virtual-network-deployment.md).