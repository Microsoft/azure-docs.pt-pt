---
title: Pontos finais de serviço de rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba como ativar o acesso direto aos recursos do Azure a partir de uma rede virtual através de pontos finais de serviço.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 8926e99db926fc8182e98509c3deff0ccc3d1612
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576672"
---
# <a name="virtual-network-service-endpoints"></a>Pontos finais de serviço de Rede Virtual

O ponto final de serviço da Rede Virtual (VNet) proporciona conectividade segura e direta aos serviços Azure durante uma rota otimizada sobre a rede de espinha dorsal Azure. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O Service Endpoints permite que os endereços IP privados no VNet cheguem ao ponto final de um serviço Azure sem precisar de um endereço IP público no VNet.

Esta funcionalidade está disponível para os seguintes serviços e regiões Azure. O recurso *da Microsoft \** está em parênteses. Ative este recurso a partir do lado da sub-rede enquanto configura os pontos finais de serviço para o seu serviço:

**Disponível em Geral**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** *(Microsoft.Storage):* Geralmente disponível em todas as regiões Azure.
- **[Base de Dados Azure SQL](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* Geralmente disponível em todas as regiões do Azure.
- **[Azure Synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql*): Geralmente disponível em todas as regiões Azure para piscinas SQL dedicadas (anteriormente SQL DW).
- **[Base de Dados Azure para servidor PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql*): Geralmente disponível nas regiões de Azure onde o serviço de base de dados está disponível.
- **[Base de Dados Azure para servidor MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql*): Geralmente disponível nas regiões de Azure onde o serviço de base de dados está disponível.
- **[Base de Dados Azure para MariaDB](../mariadb/concepts-data-access-security-vnet.md)** *(Microsoft.Sql):* Geralmente disponível nas regiões de Azure onde o serviço de base de dados está disponível.
- **[Azure Cosmos DB](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Geralmente disponível em todas as regiões do Azure.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** *(Microsoft.KeyVault*): Geralmente disponível em todas as regiões do Azure.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.ServiceBus*): Geralmente disponível em todas as regiões do Azure.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.EventHub*): Geralmente disponível em todas as regiões do Azure.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Geralmente disponível em todas as regiões do Azure onde a ADLS Gen1 está disponível.
- **[Azure App Service](../app-service/app-service-ip-restrictions.md)** *(Microsoft.Web*): Geralmente disponível em todas as regiões Azure onde o serviço de aplicações está disponível.
- **[Azure Cognitive Services](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** *(Microsoft.CognitiveServices*): Geralmente disponível em todas as regiões do Azure onde os serviços cognitivos estão disponíveis.

**Pré-visualização pública**

- **[Registo de contentores Azure](../container-registry/container-registry-vnet.md)** *(Microsoft.ContainerRegistry*): Pré-visualização disponível em regiões limitadas de Azure onde o Registo de Contentores Azure está disponível.

Para obter as notificações mais atualizadas, veja a página [Atualizações da Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Principais vantagens

Os pontos finais de serviço oferecem as seguintes vantagens:

- **Segurança melhorada para os seus recursos de serviço Azure**: os espaços de endereço privado VNet podem sobrepor-se. Não pode utilizar espaços sobrepostos para identificar de forma única o tráfego originário do seu VNet. Os pontos finais de serviço fornecem a capacidade de garantir recursos de serviço Azure à sua rede virtual, estendendo a identidade VNet ao serviço. Uma vez que ative os pontos finais do serviço na sua rede virtual, pode adicionar uma regra de rede virtual para proteger os recursos de serviço Azure à sua rede virtual. A adição de regras proporciona uma segurança melhorada, removendo totalmente o acesso público à Internet aos recursos e permitindo o tráfego apenas a partir da sua rede virtual.
- **O roteiro ideal para o tráfego de serviçoS Azure da sua rede virtual**: Hoje, quaisquer rotas na sua rede virtual que forcem o tráfego de internet para os seus locais e/ou aparelhos virtuais também forçam o tráfego de serviço Azure a tomar a mesma rota que o tráfego da internet. Os pontos finais de serviço fornecem encaminhamento ótimo para o tráfego do Azure. 

  Os pontos finais assumem sempre o tráfego de serviço diretamente a partir da rede virtual para o serviço na rede backbone do Microsoft Azure. Manter o tráfego na rede backbone do Azure permite-lhe continuar a auditar e a monitorizar o tráfego de Internet de saída das suas redes virtuais através da imposição de túnel e sem afetar o tráfego de serviço. Para obter mais informações sobre as rotas definidas pelo utilizador e a escavação forçada, consulte [o encaminhamento de tráfego de rede virtual Azure](virtual-networks-udr-overview.md).
- **Fácil de configurar com menos overhead de gestão**: já não necessita de endereços IP reservados e públicos nas redes virtuais para proteger os recursos do Azure através da firewall do IP. Não existem dispositivos de tradução de endereços de rede (NAT) ou dispositivos de gateway necessários para configurar os pontos finais de serviço. Pode configurar os pontos finais do serviço através de um simples clique numa sub-rede. Não há despesas adicionais para manter os pontos finais.

## <a name="limitations"></a>Limitações

- A funcionalidade só está disponível para redes virtuais implementadas através do modelo de implementação Azure Resource Manager.
- Os pontos finais estão ativados nas sub-redes configuradas em redes virtuais do Azure. Os pontos finais não podem ser utilizados para o tráfego das suas instalações para os serviços Azure. Para mais informações, consulte [o acesso ao serviço Secure Azure a partir do local](#secure-azure-services-to-virtual-networks)
- No Azure SQL, um ponto final de serviço aplica-se apenas ao tráfego de serviço do Azure numa região da rede virtual. Para o armazenamento Azure, os pontos finais também se estendem para incluir regiões emparelhadas onde você implanta a rede virtual para suportar o tráfego de armazenamento Read-Access Geo-Redundant (RA-GRS) e Geo-Redundant armazenamento (GRS). Para mais informações, consulte [as regiões emparelhadas Azure.](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)
- Para o Azure Data Lake Storage (ADLS) Gen 1, a capacidade de Integração VNet só está disponível para redes virtuais dentro da mesma região. Note também que a integração de rede virtual para a ADLS Gen1 utiliza a segurança do ponto final do serviço de rede virtual entre a sua rede virtual e o Azure Ative Directory (Azure AD) para gerar pedidos de segurança adicionais no token de acesso. Essas afirmações são, posteriormente, utilizadas para autenticar a rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. A tag *Microsoft.AzureActiveDirectory* listada nos pontos finais de serviço de suporte de serviços é utilizada apenas para apoiar pontos finais de serviço para a ADLS Gen 1. A Azure AD não suporta pontos finais de serviço de forma nativa. Para obter mais informações sobre a integração da Azure Data Lake Store Gen 1 VNet, consulte [a segurança da rede em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Serviços Secure Azure para redes virtuais

- Um ponto final de serviço de rede virtual transmite a identidade da sua rede virtual ao serviço do Azure. Uma vez que ative os pontos finais do serviço na sua rede virtual, pode adicionar uma regra de rede virtual para proteger os recursos de serviço Azure à sua rede virtual.
- Atualmente, o tráfego de serviço do Azure a partir de uma rede virtual utiliza endereços IP públicos como endereços IP de origem. Com pontos finais de serviço, o tráfego de serviço comuta-se para utilizar endereços privados da rede virtual como endereços IP de origem quando acede ao serviço do Azure a partir de uma rede virtual. Esta comutação permite-lhe aceder aos serviços sem necessitar de endereços IP reservados públicos utilizados nas firewalls IP.

   >[!NOTE]
   > Com pontos finais de serviço, os endereços IP de origem das máquinas virtuais na sub-rede para tráfego de serviço mudam da utilização de endereços IPv4 públicos para a utilizam de endereços IPv4 privados. As regras de firewall de serviço do Azure existentes com os endereços IP públicos do Azure deixarão de funcionar com esta troca. Certifique-se de que as regras de firewall do serviço do Azure permitem esta troca antes de configurar os pontos finais de serviço. Também poderá experienciar uma interrupção temporária do tráfego de serviço desta sub-rede ao configurar pontos finais de serviço. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Acesso de serviço Secure Azure a partir do local

  Por padrão, os recursos de serviço Azure protegidos para redes virtuais não são alcançáveis a partir de redes no local. Se pretender permitir o tráfego a partir do local, também deve permitir endereços IP públicos (tipicamente, NAT) a partir das suas instalações ou ExpressRoute. Pode adicionar estes endereços IP através da configuração de firewall IP para recursos de serviço Azure.

  ExpressRoute: Se estiver a utilizar o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para espreitar publicamente ou a Microsoft a espreitar das suas instalações, terá de identificar os endereços IP DO NAT que está a utilizar. Para espreitar publicamente, cada circuito ExpressRoute utiliza dois endereços NAT IP, por padrão, aplicados ao tráfego de serviço Azure quando o tráfego entra na espinha dorsal da rede Microsoft Azure. Para o estojo da Microsoft, os endereços NAT IP são fornecidos pelo cliente ou fornecidos pelo prestador de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Para obter mais informações sobre o NAT para o público ExpressRoute e o microsoft espreitar, consulte [os requisitos do ExpressRoute NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Proteger serviços do Azure para redes virtuais](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuração

- Configure os pontos finais de serviço numa sub-rede numa rede virtual. Os pontos finais funcionam com qualquer tipo de instância de computação em execução nessa sub-rede.
- Pode configurar vários pontos finais de serviço para todos os serviços Azure suportados (Azure Storage ou Azure SQL Database, por exemplo) numa sub-rede.
- Na Base de Dados SQL do Azure, as redes virtuais têm de estar na mesma região que o recurso de serviço do Azure. Se utilizar contas do Armazenamento do Microsoft Azure GRS e RA-GRS, a conta principal deve existir na mesma região que a rede virtual. Para todos os outros serviços, você pode garantir recursos de serviço Azure para redes virtuais em qualquer região. 
- A rede virtual onde o ponto final está configurado pode existir na mesma subscrição ou numa subscrição diferente que a do recurso de serviço do Azure. Para obter mais informações sobre as permissões necessárias para configurar pontos finais e para proteger serviços do Azure, veja [Aprovisionamento](#provisioning).
- Para os serviços suportados, pode proteger recursos novos ou existentes para redes virtuais através de pontos finais de serviço.

### <a name="considerations"></a>Considerações

- Depois de permitir um ponto final de serviço, os endereços IP de origem passam a utilizar endereços IPv4 públicos para utilizar o seu endereço IPv4 privado ao comunicar com o serviço a partir dessa sub-rede. Todas as ligações TCP abertas existentes para o serviço serão fechadas durante esta mudança. Certifique-se de que não existem tarefas críticas em execução quando ativar ou desativar um ponto final de serviço para um serviço para uma sub-rede. Verifique também se as aplicações conseguem ligar-se automaticamente aos serviços do Azure após esta mudança do endereço IP.

  A mudança do endereço IP só afeta o tráfego de serviço da rede virtual. Não há qualquer impacto em qualquer outro tráfego endereçado a ou a partir dos endereços públicos IPv4 atribuídos às suas máquinas virtuais. Nos serviços do Azure, se tiver regras de firewall existente que utilizem IPs públicos do Azure, estas regras deixam de funcionar com a mudança para os endereços privados da rede virtual.
- Com os pontos finais de serviço, as entradas de DNS para os serviços Azure permanecem como é hoje e continuam a resolver-se para endereços IP públicos atribuídos ao serviço Azure.

- Grupos de segurança de rede (NSGs) com pontos finais de serviço:
  - Por padrão, os NSGs permitem o tráfego de internet de saída e também permitem o tráfego dos seus serviços VNet para Azure. Este tráfego continua a funcionar com os pontos finais de serviço como está. 
  - Se pretender negar todo o tráfego de internet de saída e permitir apenas tráfego para serviços Azure específicos, pode fazê-lo usando [tags](./network-security-groups-overview.md#service-tags) de serviço nos seus NSGs. Pode especificar os serviços Azure suportados como destino nas suas regras NSG e a Azure também fornece a manutenção de endereços IP subjacentes a cada tag. Para obter mais informações, consulte [Etiquetas de Serviço do Azure para NSGs.](./network-security-groups-overview.md#service-tags) 

### <a name="scenarios"></a>Cenários

- **Redes virtuais múltiplas, ligadas ou ponto a ponto**: para proteger serviços do Azure em várias sub-redes numa rede virtual ou em várias redes virtuais, pode ativar pontos finais de serviço em cada uma destas sub-redes de forma independente e proteger os recursos de serviço do Azure em todas essas sub-redes.
- **Filtrar o tráfego de saída de uma rede virtual para os serviços Azure**: Se pretender inspecionar ou filtrar o tráfego enviado para um serviço Azure a partir de uma rede virtual, pode implantar um aparelho virtual de rede dentro da rede virtual. Em seguida, pode aplicar pontos finais de serviço à sub-rede onde está implementada a aplicação de rede virtual e proteger o recurso de serviço do Azure apenas nesta sub-rede. Este cenário pode ser útil se pretender utilizar a filtragem de aparelhos virtuais de rede para restringir o acesso do serviço Azure da sua rede virtual apenas a recursos específicos do Azure. Para obter mais informações, veja [saída com aplicações de rede virtual](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Garantir recursos da Azure a serviços implantados diretamente em redes virtuais**: Pode implementar diretamente vários serviços Azure em sub-redes específicas numa rede virtual. Pode proteger recursos de serviço do Azure para sub-redes de [serviços geridos](virtual-network-for-azure-services.md) ao configurar um ponto final de serviço na sub-rede do serviço gerido.
- **Tráfego de disco a partir de uma máquina virtual Azure**: O tráfego de disco de máquina virtual para discos geridos e não geridos não é afetado por alterações de encaminhamento de pontos finais de serviço para o Armazenamento Azure. Este tráfego inclui diskIO, bem como montagem e desmontagem. Pode limitar o acesso do REST às bolhas de página para selecionar redes através de pontos finais de serviço e [regras de rede de armazenamento Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Registo e resolução de problemas

Uma vez configurar os pontos finais de serviço para um serviço específico, valide que a rota do ponto final de serviço está em vigor por: 
 
- Validação do endereço IP de origem de qualquer pedido de serviço no diagnóstico do serviço. Todos os novos pedidos com pontos finais de serviço mostram o endereço IP de origem para o pedido como o endereço IP privado da rede virtual atribuído ao cliente que efetua o pedido a partir da sua rede virtual. Sem o ponto final, o endereço é um endereço IP público do Azure.
- Visualização das rotas efetivas em qualquer interface de rede numa sub-rede. A rota para o serviço:
  - Mostra uma rota predefinida mais específica para endereçar intervalos de prefixo de cada serviço
  - Tem um nextHopType de *VirtualNetworkServiceEndpoint*
  - Indica que uma ligação mais direta ao serviço está em vigor em comparação com quaisquer rotas de túneis forçados

>[!NOTE]
> As rotas do ponto final de serviço substituem quaisquer rotas BGP ou UDR para a correspondência de prefixo de endereço de um serviço do Azure. Para mais informações, consulte [a resolução de problemas com rotas eficazes.](diagnose-network-routing-problem.md)

## <a name="provisioning"></a>Aprovisionamento

Os pontos finais de serviço podem ser configurados em redes virtuais de forma independente por um utilizador com acesso por escrito a uma rede virtual. Para garantir os recursos de serviço do Azure a um VNet, o utilizador deve ter permissão para *microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* para as sub-redes adicionadas. As funções de administrador de serviço incorporado incluem esta permissão por padrão. Pode modificar a permissão criando funções personalizadas.

Para obter mais informações sobre papéis incorporados, consulte [as funções incorporadas do Azure.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Para obter mais informações sobre a atribuição de permissões específicas a funções personalizadas, consulte [as funções personalizadas Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As redes virtuais e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Certos Serviços Azure (nem todos) como o Azure Storage e o Azure Key Vault também suportam pontos finais de serviço em diferentes inquilinos ative directory(AD), ou seja, a rede virtual e o recurso de serviço Azure podem estar em diferentes inquilinos ative directory (AD). Verifique a documentação individual do serviço para mais detalhes.  

## <a name="pricing-and-limits"></a>Preços e limites

Não há nenhuma taxa adicional para usar pontos finais de serviço. O atual modelo de preços para os serviços Azure (Azure Storage, Azure SQL Database, etc.) aplica-se como é hoje.

Não há limite para o número total de pontos finais de serviço numa rede virtual.

Certos serviços Azure, tais como contas de armazenamento Azure, podem impor limites ao número de sub-redes utilizadas para assegurar o recurso. Consulte a documentação para vários serviços na secção [etapas seguintes](#next-steps) para obter mais detalhes.

## <a name="vnet-service-endpoint-policies"></a>Políticas de ponto final de serviço VNet 

As políticas de ponto final do serviço VNet permitem filtrar o tráfego de rede virtual para os serviços Azure. Este filtro permite apenas recursos de serviço Azure específicos sobre os pontos finais de serviço. As políticas de ponto final de serviço fornecem controlo de acesso granular para tráfego de rede virtual para serviços Azure. Para obter mais informações, consulte [as Políticas de Ponto final do Serviço de Rede Virtual.](./virtual-network-service-endpoint-policies-overview.md)

## <a name="faqs"></a>FAQs

Para perguntas frequentes, consulte [as FAQs de endpoint do serviço de rede virtual.](./virtual-networks-faq.md#virtual-network-service-endpoints)

## <a name="next-steps"></a>Passos seguintes

- [Configurar pontos finais de serviço de rede virtual](tutorial-restrict-network-access-to-resources.md)
- [Garantir uma conta de Armazenamento Azure a uma rede virtual](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Garantir uma Base de Dados Azure SQL a uma rede virtual](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Garantir um Azure Synapse Analytics a uma rede virtual](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integração de serviços Azure em redes virtuais](virtual-network-for-azure-services.md)
- [Políticas de ponto final de serviço de rede virtual](./virtual-network-service-endpoint-policies-overview.md)
- [Modelo Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)
