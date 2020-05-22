---
title: Controlar o tráfego da rede em Azure HDInsight
description: Aprenda técnicas para controlar o tráfego de entrada e saída para os clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 54a55789cf867c97cf2384b48f1e5545ee54dafc
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773411"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Controlar o tráfego da rede em Azure HDInsight

O tráfego de rede numa Rede Virtual Azure pode ser controlado utilizando os seguintes métodos:

* **Os grupos** de segurança da rede (NSG) permitem filtrar o tráfego de entrada e saída para a rede. Para obter mais informações, consulte o tráfego da rede Filter com documento [de grupos](../virtual-network/security-overview.md) de segurança da rede.

* **Os aparelhos virtuais** da rede (NVA) só podem ser utilizados com tráfego de saída. Os NVAs replicam a funcionalidade de dispositivos como firewalls e routers. Para mais informações, consulte o documento [de Aparelhos de Rede.](https://azure.microsoft.com/solutions/network-appliances)

Como um serviço gerido, o HDInsight requer acesso ilimitado aos serviços de saúde e gestão HDInsight, tanto para o tráfego de entrada como para o tráfego de saída do VNET. Ao utilizar NSGs, deve garantir que estes serviços ainda podem comunicar com o cluster HDInsight.

![Diagrama de entidades HDInsight criadas em VNET personalizado Azure](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>HDInsight com grupos de segurança de rede

Se planeia utilizar **grupos** de segurança de rede para controlar o tráfego da rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifique a região azure que pretende utilizar para o HDInsight.

2. Identifique as etiquetas de serviço exigidas pelo HDInsight para a sua região. Para mais informações, consulte as etiquetas de serviço do Grupo de [Segurança da Rede (NSG) para o Azure HDInsight](hdinsight-service-tags.md).

3. Crie ou modifique os grupos de segurança da rede para a subnet a que planeia instalar o HDInsight.

    * __Grupos__de segurança da rede : permitir o tráfego __de entrada__ no porto __443__ a partir dos endereços IP. Isto garantirá que os serviços de gestão HDInsight possam chegar ao cluster de fora da rede virtual. Para os clusters ativados por __proxy Kafka REST,__ permita o tráfego __de entrada__ no porto __9400__ também. Isto garantirá que o servidor proxy Kafka REST seja acessível.

Para obter mais informações sobre os grupos de segurança da rede, consulte a [visão geral dos grupos de segurança da rede](../virtual-network/security-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controlar o tráfego de saída dos clusters HDInsight

Para obter mais informações sobre o controlo do tráfego de saída dos clusters HDInsight, consulte a restrição de tráfego de rede de [saída da Configure para os clusters Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Túneis forçados para o local

A escavação forçada é uma configuração de encaminhamento definida pelo utilizador onde todo o tráfego de uma subnet é forçado a uma rede ou localização específica, como a sua rede no local ou firewall. A escavação forçada de toda a transferência de dados de volta ao local _não_ é recomendada devido a grandes volumes de transferência de dados e potencial impacto de desempenho.

Os clientes interessados em configurar túneis forçados, devem utilizar [metastores personalizados](./hdinsight-use-external-metadata-stores.md) e configurar a conectividade adequada desde a subnet de cluster ou rede no local até estas metastores personalizadas.

Para ver um exemplo da configuração udr com firewall Azure, consulte a restrição de tráfego de [rede de saída do Configure para os clusters Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ip-addresses"></a>Endereços IP necessários

Se utilizar grupos de segurança de rede ou rotas definidas pelo utilizador para controlar o tráfego, consulte os endereços IP de [gestão HDInsight](hdinsight-management-ip-addresses.md).

## <a name="required-ports"></a>Portas necessárias

Se planeia utilizar uma **firewall** e aceder ao cluster de fora em certas portas, poderá ter de permitir o tráfego nas portas necessárias para o seu cenário. Por predefinição, não é necessária uma lista especial de portas, desde que o tráfego de gestão azure explicado na secção anterior seja autorizado a atingir o cluster no porto 443.

Para obter uma lista de portas para serviços específicos, consulte as [Portas utilizadas pelos serviços Apache Hadoop no documento HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

Para obter mais informações sobre as regras de firewall para aparelhos virtuais, consulte o documento do [cenário do aparelho virtual.](../virtual-network/virtual-network-scenario-udr-gw-nva.md)

## <a name="next-steps"></a>Passos seguintes

* Para amostras de código e exemplos de criação de Redes Virtuais Azure, consulte [Criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* Para um exemplo de configuração de HDInsight para ligar a uma rede no local, consulte [O Connect HDInsight para uma rede no local](./connect-on-premises-network.md).
* Para obter mais informações sobre as redes virtuais do Azure, consulte a [visão geral da Rede Virtual Azure.](../virtual-network/virtual-networks-overview.md)
* Para obter mais informações sobre grupos de segurança da rede, consulte [grupos](../virtual-network/security-overview.md)de segurança da rede .
* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte as rotas definidas pelo utilizador e o [reencaminhamento ip](../virtual-network/virtual-networks-udr-overview.md).
* Para obter mais informações sobre redes virtuais, consulte [O Plano VNETs para HDInsight](./hdinsight-plan-virtual-network-deployment.md).
