---
title: Rede virtual para serviços do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre os benefícios da implantação de recursos em uma rede virtual. Os recursos em redes virtuais podem se comunicar entre si e recursos locais, sem tráfego atravessando a Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: c345b31c218c4678e7811c36113c94e0c4d2ac03
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019063"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integração de rede virtual para serviços do Azure

A integração dos serviços do Azure a uma rede virtual do Azure permite o acesso privado ao serviço de máquinas virtuais ou recursos de computação na rede virtual.
Você pode integrar os serviços do Azure em sua rede virtual com as seguintes opções:
- Implantando instâncias dedicadas do serviço em uma rede virtual. Os serviços podem então ser acessados de forma privada na rede virtual e em redes locais.
- Usando o [link privado](../private-link/private-link-overview.md) para acessar privadamente uma instância específica do serviço de sua rede virtual e de redes locais.

Você também pode acessar o serviço usando pontos de extremidade públicos estendendo uma rede virtual para o serviço, por meio de [pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md). Os pontos de extremidade de serviço permitem que os recursos de serviço sejam protegidos para a rede virtual.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Implantar serviços do Azure em redes virtuais

Ao implantar serviços dedicados do Azure em uma [rede virtual](virtual-networks-overview.md), você pode se comunicar com os recursos de serviço de forma privada, por meio de endereços IP privados.

![Serviços implantados em uma rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A implantação de serviços em uma rede virtual fornece os seguintes recursos:

- Os recursos na rede virtual podem se comunicar entre si de forma privada, por meio de endereços IP privados. Por exemplo, transferir dados diretamente entre o HDInsight e o SQL Server em execução em uma máquina virtual, na rede virtual.
- Os recursos locais podem acessar recursos em uma rede virtual usando endereços IP privados em uma [VPN site a site (gateway de VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- As redes virtuais podem [](virtual-network-peering-overview.md) ser emparelhadas para permitir que os recursos nas redes virtuais se comuniquem entre si, usando endereços IP privados.
- As instâncias de serviço em uma rede virtual normalmente são totalmente gerenciadas pelo serviço do Azure. Isso inclui o monitoramento da integridade dos recursos e o dimensionamento com carga.
- As instâncias de serviço são implantadas em uma sub-rede em uma rede virtual. O acesso à rede de entrada e saída para a sub-rede deve ser aberto por meio de [grupos de segurança de rede](security-overview.md#network-security-groups), de acordo com as diretrizes fornecidas pelo serviço.
- Determinados serviços também impõem restrições à sub-rede em que são implantados, limitando a aplicação de políticas, rotas ou combinando VMs e recursos de serviço na mesma sub-rede. Verifique cada serviço nas restrições específicas, pois elas podem mudar ao longo do tempo. Exemplos desses serviços são Azure NetApp Files, HSM dedicado, instâncias de contêiner do Azure, serviço de aplicativo. 
- Opcionalmente, os serviços podem exigir uma [sub-rede delegada](virtual-network-manage-subnet.md#add-a-subnet) como um identificador explícito que uma sub-rede pode hospedar um serviço específico. Ao delegar, os serviços obtêm permissões explícitas para criar recursos específicos do serviço na sub-rede delegada.
- Veja um exemplo de uma resposta da API REST em uma [rede virtual com uma sub-rede delegada](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Uma lista abrangente de serviços que estão usando o modelo de sub-rede delegada pode ser obtida por meio da API de [delegações disponíveis](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) .

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Serviços que podem ser implantados em uma rede virtual

|Category|Serviço| Sub-rede ¹ dedicada
|-|-|-|
| Computação | Máquinas virtuais: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Serviço de nuvem](https://msdn.microsoft.com/library/azure/jj156091): Somente rede virtual (clássica)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Não <br/> Não <br/> Não <br/> Sem ²
| Rede | [Gateway de aplicativo-WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Dispositivos de rede virtual](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Sim <br/> Sim <br/> Sim <br/> Não
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância gerida da base de dados SQL do Azure](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Sim <br/> Sim <br/> 
|Análise | [O Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Sem ² <br/> Sem ² <br/> 
| identidade | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Não <br/>
| Contentores | [Serviço Kubernetes do Azure (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância de contêiner do Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Mecanismo do serviço de contêiner do Azure](https://github.com/Azure/acs-engine) com o [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI de rede virtual do Azure|Sem ²<br/> Sim <br/><br/> Não
| Web | [Gestão de API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente do Serviço de Aplicações](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/> Sim
| Alojado | [HSM dedicado do Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/>
| | |

¹ ' Dedicated ' implica que apenas recursos específicos de serviço podem ser implantados nesta sub-rede e não podem ser combinados com VM/VMSSs do cliente <br/> ² recomendado, mas não um requisito obrigatório imposto pelo serviço.
