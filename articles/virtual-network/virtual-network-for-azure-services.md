---
title: Rede virtual para serviços do Azure
titlesuffix: Azure Virtual Network
description: Aprenda como implementar serviços Azure dedicados numa rede virtual e conheça as capacidades que essas implementações fornecem.
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: 79b6fa1043b1bb8add6b6beb5fd38312ee12ea75
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210279"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Implementar serviços dedicados da Azure em redes virtuais

Quando implementa serviços Azure dedicados numa [rede virtual,](virtual-networks-overview.md)pode comunicar com os recursos de serviço em privado, através de endereços IP privados.

![Serviços implantados numa rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A implantação de serviços dentro de uma rede virtual fornece as seguintes capacidades:

- Os recursos dentro da rede virtual podem comunicar-se entre si em privado, através de endereços IP privados. Exemplo, transferindo diretamente dados entre o HDInsight e o SQL Server a funcionar numa máquina virtual, na rede virtual.
- Os recursos no local podem aceder a recursos numa rede virtual utilizando endereços IP privados através de uma [VPN site-to-site (VPN Gateway)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- As redes virtuais podem ser [espreitadas](virtual-network-peering-overview.md) para permitir que os recursos nas redes virtuais se comuniquem entre si, utilizando endereços IP privados.
- As instâncias de serviço numa rede virtual são normalmente totalmente geridas pelo serviço Azure. Isto inclui monitorizar a saúde dos recursos e escalar com carga.
- As instâncias de serviço são implantadas numa sub-rede numa rede virtual. O acesso à rede de entrada e saída da sub-rede deve ser aberto através de [grupos de segurança de rede,](./network-security-groups-overview.md#network-security-groups)de acordo com as orientações fornecidas pelo serviço.
- Certos serviços impõem também restrições à sub-rede em que são implantados, limitando a aplicação de políticas, rotas ou combinando VMs e recursos de serviço dentro da mesma sub-rede. Consulte cada serviço sobre as restrições específicas, uma vez que podem mudar ao longo do tempo. Exemplos destes serviços são ficheiros Azure NetApp, HSM dedicado, Instâncias de Contentores Azure, Serviço de Aplicações. 
- Opcionalmente, os serviços podem exigir uma [sub-rede delegada](virtual-network-manage-subnet.md#add-a-subnet) como um identificador explícito de que uma sub-rede pode hospedar um determinado serviço. Ao delegar, os serviços obtêm permissões explícitas para criar recursos específicos do serviço na sub-rede delegada.
- Veja um exemplo de uma resposta da API REST numa [rede virtual com uma sub-rede delegada.](/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet) Uma lista completa de serviços que utilizam o modelo de sub-redes delegado pode ser obtida através da API [das Delegações Disponíveis.](/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Serviços que podem ser implementados numa rede virtual

|Categoria|Serviço| 1<sup></sup> Subneta dedicada
|-|-|-|
| Computação | Máquinas virtuais: [Linux](/previous-versions/azure/virtual-machines/linux/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](/previous-versions/azure/virtual-machines/windows/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Serviço cloud](/previous-versions/azure/reference/jj156091(v=azure.100)): Rede virtual (clássica) apenas<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Não <br/> Não <br/> Não <br/> Nº<sup>2</sup>
| Rede | [Gateway de aplicação - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Aparelhos virtuais de rede](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| Sim <br/> Sim <br/> Sim <br/> Sim <br/> Não
|Dados|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância Gerida do SQL no Azure](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Sim <br/> Sim <br/> 
|Análise | [Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nº<sup>2</sup> <br/> Nº<sup>2</sup> <br/> 
| Identidade | [Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Não <br/>
| Contentores | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância do contentor Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Motor de serviço de contentores Azure](https://github.com/Azure/acs-engine) com [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI de rede virtual Azure<br/>[Funções do Azure](../azure-functions/functions-networking-options.md#virtual-network-integration) |Nº<sup>2</sup><br/> Sim <br/> Não <br/> Sim
| Web | [Gestão de API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Aplicações Web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente do Serviço de Aplicações](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/> Sim <br/> Sim
| Alojado | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/>
| Azure Spring Cloud | [Implantação na rede virtual Azure (injeção VNet)](../spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network.md)<br/>| Sim <br/>
| | |

<sup>1</sup> 'Dedicado' implica que apenas os recursos específicos do serviço podem ser implantados nesta sub-rede e não podem ser combinados com vM/VMSS do cliente <br/> 
<sup>2</sup> É recomendado como uma boa prática ter estes serviços numa sub-rede dedicada, mas não um requisito obrigatório imposto pelo serviço.
