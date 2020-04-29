---
title: Rede virtual para serviços do Azure
titlesuffix: Azure Virtual Network
description: Conheça os benefícios de implantar recursos numa rede virtual. Os recursos em redes virtuais podem comunicar entre si, e no local, sem que o tráfego atravesse a Internet.
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
ms.openlocfilehash: 70266a1280b90b4573073d633a918f701f9ee8c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878277"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Implementar serviços Azure dedicados em redes virtuais

Quando implementa serviços Azure dedicados numa [rede virtual,](virtual-networks-overview.md)pode comunicar com os recursos de serviço em privado, através de endereços IP privados.

![Serviços implantados numa rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A implantação de serviços dentro de uma rede virtual fornece as seguintes capacidades:

- Os recursos dentro da rede virtual podem comunicar entre si em privado, através de endereços IP privados. Exemplo, transferir diretamente dados entre o HDInsight e o SQL Server a funcionar numa máquina virtual, na rede virtual.
- Os recursos no local podem aceder a recursos numa rede virtual utilizando endereços IP privados através de uma [VPN site-to-site (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- As redes virtuais podem ser [esponjadas](virtual-network-peering-overview.md) para permitir que os recursos nas redes virtuais se comuniquem entre si, utilizando endereços IP privados.
- As instâncias de serviço numa rede virtual são normalmente geridas totalmente pelo serviço Azure. Isto inclui monitorizar a saúde dos recursos e escalonar com carga.
- As instâncias de serviço são implantadas numa subnet numa rede virtual. O acesso à rede de entrada e saída para a subrede deve ser aberto através de grupos de segurança da [rede,](security-overview.md#network-security-groups)por orientação prestada pelo serviço.
- Alguns serviços também impõem restrições à sub-rede em que são implantados, limitando a aplicação de políticas, rotas ou combinando VMs e recursos de serviço dentro da mesma subnet. Verifique com cada serviço as restrições específicas, uma vez que podem alterar ao longo do tempo. Exemplos destes serviços são ficheiros Azure NetApp, HSM dedicado, Instâncias de Contentores Azure, Serviço de Aplicações. 
- Opcionalmente, os serviços podem exigir uma [sub-rede delegada](virtual-network-manage-subnet.md#add-a-subnet) como um identificador explícito que uma subnet pode acolher um determinado serviço. Ao delegar, os serviços obtêm permissões explícitas para criar recursos específicos do serviço na sub-rede delegada.
- Veja um exemplo de uma resposta REST API numa [rede virtual com uma subnet delegada](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Uma lista completa de serviços que utilizam o modelo de sub-rede delegado pode ser obtida através da API das [Delegações Disponíveis.](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Serviços que podem ser implementados numa rede virtual

|Categoria|Serviço| Dedicado<sup>1</sup>sup</sup>>1 Subnet
|-|-|-|
| Computação | Máquinas virtuais: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Serviço cloud](https://msdn.microsoft.com/library/azure/jj156091): Rede virtual (clássica) apenas<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Não <br/> Não <br/> Não <br/> No<sup>2</sup>sup>2</sup>
| Rede | [Gateway de aplicações - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Aparelhos virtuais da rede](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Sim <br/> Sim <br/> Sim <br/> Não
|Dados|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância Gerida da Base de Dados SQL do Azure](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Sim <br/> Sim <br/> 
|Análise | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No<sup>2</sup>sup>2</su<sup>2</sup>> <br/> Nº<sup>2</sup> <br/> 
| Identidade | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Não <br/>
| Contentores | [Serviço de Kubernetes do Azure (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância do Azure Container Instances (ACI)](https://www.aka.ms/acivnet)<br/>Motor de serviço de [contentores Azure](https://github.com/Azure/acs-engine) com [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI rede virtual Azure<br/>[Funções do Azure](../azure-functions/functions-networking-options.md#virtual-network-integration) |No<sup>2</sup>sup>2</sup><br/> Sim <br/><br/> Não <br/> Sim
| Web | [Gestão de API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Aplicações Web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente do Serviço de Aplicações](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/> Sim <br/> Sim
| Hospedado | [HSM Dedicado do Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/>
| | |

<sup>1</sup> 'Dedicado' implica que apenas os recursos específicos do serviço podem ser implantados nesta subnet e não podem ser combinados com vM/VMSS simesmos do cliente <br/> 
<sup>2</sup> É recomendado como uma melhor prática ter estes serviços numa subrede dedicada, mas não uma exigência obrigatória imposta pelo serviço.
