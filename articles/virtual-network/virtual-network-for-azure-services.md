---
title: Rede virtual para serviços do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre os benefícios de implantar recursos numa rede virtual. Recursos em redes virtuais podem comunicar entre si e recursos no local, sem o tráfego que atravessa a Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: 357122abba483a96dd9f2d602a793ee06e80b245
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785542"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integração da rede virtual para serviços do Azure

Integração de serviços do Azure a uma rede virtual do Azure permite o acesso privado para o serviço de máquinas virtuais ou recursos de computação na rede virtual.
Pode integrar os serviços do Azure na sua rede virtual com as seguintes opções:
- Implementar instâncias dedicadas do serviço numa rede virtual. Os serviços, em seguida, podem ser acedidos em privado dentro da rede virtual e de redes no local.
- Estendendo uma rede virtual para o serviço, através de pontos finais de serviço. Pontos finais de serviço permitem que os recursos de serviço individual a ser protegido para a rede virtual.

Para integrar vários serviços do Azure à sua rede virtual, pode combinar um ou mais dos padrões acima. Por exemplo, pode implementar o HDInsight numa rede virtual e proteger uma conta de armazenamento para a sub-rede do HDInsight através de pontos finais de serviço.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Implementar serviços do Azure em redes virtuais

Ao implementar os serviços do Azure dedicados num [rede virtual](virtual-networks-overview.md), pode se comunicar com os recursos de serviço em privado, através de endereços IP privados.

![Serviços implementados numa rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Implementação de serviços dentro de uma rede virtual fornece as seguintes capacidades:

- Recursos dentro da rede virtual podem comunicar entre si em privado, através de endereços IP privados. Exemplo, transferência de dados diretamente entre o HDInsight e o SQL Server em execução numa máquina virtual, na rede virtual.
- No local recursos podem aceder a recursos numa rede virtual com endereços IP privados através de um [VPN de Site a Site (Gateway de VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Redes virtuais podem estar [em modo de peering](virtual-network-peering-overview.md) para ativar a recursos em redes virtuais para comunicar entre si, através de IP privado de endereços.
- Instâncias de serviço numa rede virtual, normalmente, são totalmente geridas pelo serviço do Azure. Isto inclui o estado de funcionamento dos recursos de monitorização e dimensionamento com a carga.
- Instâncias de serviço são implementadas numa sub-rede numa rede virtual. Entrada e acesso de rede de saída para a sub-rede tem de ser aberto por meio [grupos de segurança de rede](security-overview.md#network-security-groups), com orientação fornecida pelo serviço.
- Determinados serviços também impõem restrições na sub-rede que são implementados, limitar a aplicação de políticas, rotas ou combinar VMs e recursos de serviço dentro da mesma sub-rede. Verifique com cada serviço nas restrições específicas, como eles podem ser alterados ao longo do tempo. Exemplos de tais serviços são ficheiros do Azure NetApp, HSM dedicados, Azure Container Instances, serviço de aplicações. 
- Opcionalmente, os serviços podem exigir um [delegado sub-rede](virtual-network-manage-subnet.md#add-a-subnet) como um identificador explícito que uma sub-rede pode hospedar um serviço específico. Ao delegar, serviços obtém permissões explícitas para criar recursos específicos do serviço na sub-rede de delegado.
- Ver um exemplo de uma resposta de REST API num [rede virtual com uma sub-rede de delegado](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Uma lista abrangente de serviços que estão a utilizar o modelo de sub-rede delegados pode ser obtida através da [delegações disponíveis](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Serviços que podem ser implementados numa rede virtual

|Category|Serviço| Dedicated¹ sub-rede
|-|-|-|
| Computação | Máquinas virtuais: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Serviço em nuvem](https://msdn.microsoft.com/library/azure/jj156091): Rede virtual (clássico) apenas<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Não <br/> Não <br/> Não <br/> No²
| Rede | [Gateway de aplicação - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Aplicações virtuais de rede](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Sim <br/> Sim <br/> Sim <br/> Não
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância gerida da base de dados SQL do Azure](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Sim <br/> Sim <br/> 
|Análise | [O Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| identidade | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Não <br/>
| Contentores | [Serviço Kubernetes do Azure (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância de contentor do Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Motor do serviço de contentor do Azure](https://github.com/Azure/acs-engine) com CNI de rede Virtual do Azure [Plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|No²<br/> Sim <br/><br/> Não
| Web | [Gestão de API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente do Serviço de Aplicações](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/> Sim
| Hosted | [HSM dedicados do Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ficheiros do Azure NetApp](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/>
| | |

¹ "dedicado" significa que apenas recursos específicos do serviço podem ser implementados nesta sub-rede e não podem ser combinados com o cliente VM/VMSSs <br/> Recomendado ², mas não um requisito obrigatório impostas pelo serviço.


## <a name="service-endpoints-for-azure-services"></a>Pontos finais de serviço para serviços do Azure

Alguns serviços do Azure não não possível implementar em redes virtuais. Pode restringir o acesso a alguns dos recursos de serviço para sub-redes de rede virtual apenas específico, se optar por ativar um ponto de final de serviço de rede virtual.  Saiba mais sobre [pontos finais de serviço de rede virtual](virtual-network-service-endpoints-overview.md)e os serviços que, pontos de extremidade podem ser ativados para.
