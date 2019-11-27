---
title: Pontos de extremidade de serviço de rede virtual do Azure
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
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378697"
---
# <a name="virtual-network-service-endpoints"></a>Pontos finais de serviço de Rede Virtual

Os pontos de extremidade de serviço de rede virtual (VNet) estendem o espaço de endereço privado da rede virtual. Os pontos de extremidade também estendem a identidade de sua VNet para os serviços do Azure por meio de uma conexão direta. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O tráfego da sua VNet para o serviço do Azure permanece sempre na rede backbone do Microsoft Azure.

Esse recurso está disponível para os seguintes serviços e regiões do Azure. O recurso *Microsoft.\** está entre parênteses. Habilite esse recurso do lado da sub-rede ao configurar pontos de extremidade de serviço para seu serviço:

**Disponível em Geral**

- **[Armazenamento do Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft. Storage*): geralmente disponível em todas as regiões do Azure.
- **[Banco de dados SQL do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): geralmente disponível em todas as regiões do Azure.
- **[SQL data warehouse do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): geralmente disponível em todas as regiões do Azure.
- **[Banco de dados do Azure para servidor PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): geralmente disponível em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Banco de dados do Azure para servidor MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): geralmente disponível em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Banco de dados do Azure para MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft. SQL*): geralmente disponível em regiões do Azure em que o serviço de banco de dados está disponível.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureCosmosDB*): geralmente disponível em todas as regiões do Azure.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft. keyvault*): geralmente disponível em todas as regiões do Azure.
- **[Barramento de serviço do Azure](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. ServiceBus*): geralmente disponível em todas as regiões do Azure.
- **[Hubs de eventos do Azure](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. EventHub*): geralmente disponíveis em todas as regiões do Azure.
- **[Azure data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureActiveDirectory*): geralmente disponível em todas as regiões do Azure em que ADLS Gen1 está disponível.
- **[Azure app serviço](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : geralmente disponível em todas as regiões do Azure em que o serviço de aplicativo está disponível.

**Visualização pública**

- **[Registro de contêiner do Azure](../container-registry/container-registry-vnet.md)** (*Microsoft. ContainerRegistry*): visualização disponível em todas as regiões do Azure em que o registro de contêiner do Azure está disponível.

Para obter as notificações mais atualizadas, veja a página [Atualizações da Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Principais vantagens

Os pontos finais de serviço oferecem as seguintes vantagens:

- **Segurança aprimorada para seus recursos de serviço do Azure**: espaços de endereço privado de VNet podem se sobrepor. Não é possível usar espaços sobrepostos para identificar exclusivamente o tráfego originado de sua VNet. Os pontos de extremidade de serviço fornecem a capacidade de proteger os recursos de serviço do Azure para sua rede virtual, estendendo a identidade de VNet para o serviço. Depois de habilitar os pontos de extremidade de serviço em sua rede virtual, você pode adicionar uma regra de rede virtual para proteger os recursos de serviço do Azure para sua rede virtual. A adição de regra fornece segurança aprimorada removendo totalmente o acesso à Internet pública aos recursos e permitindo o tráfego somente de sua rede virtual.
- **Roteamento ideal para o tráfego de serviço do Azure de sua rede virtual**: hoje, todas as rotas em sua rede virtual que forçam o tráfego de Internet para seus dispositivos locais e/ou virtuais também forçam o tráfego de serviço do Azure a obter a mesma rota que o tráfego de Internet. Os pontos finais de serviço fornecem encaminhamento ótimo para o tráfego do Azure. 

  Os pontos finais assumem sempre o tráfego de serviço diretamente a partir da rede virtual para o serviço na rede backbone do Microsoft Azure. Manter o tráfego na rede backbone do Azure permite-lhe continuar a auditar e a monitorizar o tráfego de Internet de saída das suas redes virtuais através da imposição de túnel e sem afetar o tráfego de serviço. Para obter mais informações sobre rotas definidas pelo usuário e túnel forçado, consulte [Roteamento de tráfego de rede virtual do Azure](virtual-networks-udr-overview.md).
- **Fácil de configurar com menos overhead de gestão**: já não necessita de endereços IP reservados e públicos nas redes virtuais para proteger os recursos do Azure através da firewall do IP. Não há nenhum NAT (conversão de endereços de rede) ou dispositivos de gateway necessários para configurar os pontos de extremidade de serviço. Você pode configurar pontos de extremidade de serviço por meio de um simples clique em uma sub-rede. Não há nenhuma sobrecarga adicional para manter os pontos de extremidade.

## <a name="limitations"></a>Limitações

- A funcionalidade só está disponível para redes virtuais implementadas através do modelo de implementação Azure Resource Manager.
- Os pontos finais estão ativados nas sub-redes configuradas em redes virtuais do Azure. Os pontos de extremidade não podem ser usados para o tráfego de seu local para os serviços do Azure. Para obter mais informações, consulte [proteger o acesso do serviço do Azure do local](#secure-azure-services-to-virtual-networks)
- No Azure SQL, um ponto final de serviço aplica-se apenas ao tráfego de serviço do Azure numa região da rede virtual. Para o armazenamento do Azure, os pontos de extremidade também se estendem para incluir regiões emparelhadas em que você implanta a rede virtual para oferecer suporte ao tráfego de armazenamento com redundância geográfica (RA-GRS) e armazenamento com redundância geográfica (GRS). Para obter mais informações, consulte [regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Para Azure Data Lake Storage (ADLS) Gen 1, o recurso de integração VNet só está disponível para redes virtuais na mesma região. Observe também que a integração de rede virtual para ADLS Gen1 usa a segurança do ponto de extremidade do serviço de rede virtual entre sua rede virtual e Azure Active Directory (AD do Azure) para gerar declarações de segurança adicionais no token de acesso. Essas afirmações são, posteriormente, utilizadas para autenticar a rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. A marca *Microsoft. AzureActiveDirectory* listada em serviços que dão suporte a pontos de extremidade de serviço é usada somente para dar suporte a pontos de extremidade de serviço para o ADLS Gen 1. O Azure AD não dá suporte a pontos de extremidade de serviço nativamente. Para obter mais informações sobre a integração de VNet do Azure Data Lake Store Gen 1, consulte [segurança de rede em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Proteger serviços do Azure para redes virtuais

- Um ponto final de serviço de rede virtual transmite a identidade da sua rede virtual ao serviço do Azure. Depois de habilitar os pontos de extremidade de serviço em sua rede virtual, você pode adicionar uma regra de rede virtual para proteger os recursos de serviço do Azure para sua rede virtual.
- Atualmente, o tráfego de serviço do Azure a partir de uma rede virtual utiliza endereços IP públicos como endereços IP de origem. Com pontos finais de serviço, o tráfego de serviço comuta-se para utilizar endereços privados da rede virtual como endereços IP de origem quando acede ao serviço do Azure a partir de uma rede virtual. Esta comutação permite-lhe aceder aos serviços sem necessitar de endereços IP reservados públicos utilizados nas firewalls IP.

   >[!NOTE]
   > Com pontos finais de serviço, os endereços IP de origem das máquinas virtuais na sub-rede para tráfego de serviço mudam da utilização de endereços IPv4 públicos para a utilizam de endereços IPv4 privados. As regras de firewall de serviço do Azure existentes com os endereços IP públicos do Azure deixarão de funcionar com esta troca. Certifique-se de que as regras de firewall do serviço do Azure permitem esta troca antes de configurar os pontos finais de serviço. Também poderá experienciar uma interrupção temporária do tráfego de serviço desta sub-rede ao configurar pontos finais de serviço. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Proteger o acesso do serviço do Azure do local

  Por padrão, os recursos de serviço do Azure protegidos para redes virtuais não são acessíveis de redes locais. Se você quiser permitir o tráfego local, também deverá permitir endereços IP públicos (normalmente, NAT) do seu local ou do ExpressRoute. Você pode adicionar esses endereços IP por meio da configuração de firewall IP para recursos de serviço do Azure.

  ExpressRoute: se você estiver usando o [expressroute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para emparelhamento público ou emparelhamento da Microsoft de seu local, será necessário identificar os endereços IP de NAT que você está usando. Para o emparelhamento público, cada circuito do ExpressRoute usa dois endereços IP NAT, por padrão, aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede Microsoft Azure. Para o emparelhamento da Microsoft, os endereços IP de NAT são fornecidos pelo cliente ou fornecidos pelo provedor de serviços. Para permitir o acesso aos recursos de serviço, você deve permitir esses endereços IP públicos na configuração de firewall de IP do recurso. Para localizar os endereços IP do circuito do ExpressRoute de emparelhamento público, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do portal do Azure. Para obter mais informações sobre NAT para emparelhamento público e da Microsoft para o ExpressRoute, consulte [requisitos de NAT do expressroute](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Proteger serviços do Azure para redes virtuais](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuração

- Configurar pontos de extremidade de serviço em uma sub-rede em uma rede virtual. Os pontos finais funcionam com qualquer tipo de instância de computação em execução nessa sub-rede.
- Você pode configurar vários pontos de extremidade de serviço para todos os serviços do Azure com suporte (armazenamento do Azure ou banco de dados SQL do Azure, por exemplo) em uma sub-rede.
- Na Base de Dados SQL do Azure, as redes virtuais têm de estar na mesma região que o recurso de serviço do Azure. Se utilizar contas do Armazenamento do Microsoft Azure GRS e RA-GRS, a conta principal deve existir na mesma região que a rede virtual. Para todos os outros serviços, você pode proteger os recursos de serviço do Azure para redes virtuais em qualquer região. 
- A rede virtual onde o ponto final está configurado pode existir na mesma subscrição ou numa subscrição diferente que a do recurso de serviço do Azure. Para obter mais informações sobre as permissões necessárias para configurar pontos finais e para proteger serviços do Azure, veja [Aprovisionamento](#provisioning).
- Para os serviços suportados, pode proteger recursos novos ou existentes para redes virtuais através de pontos finais de serviço.

### <a name="considerations"></a>Considerações

- Depois de habilitar um ponto de extremidade de serviço, os endereços IP de origem das máquinas virtuais na chave de sub-rede. Os endereços IP de origem são alternados do uso de endereços IPv4 públicos para usar seu endereço IPv4 privado ao se comunicar com o serviço dessa sub-rede. Todas as ligações TCP abertas existentes para o serviço serão fechadas durante esta mudança. Certifique-se de que não existem tarefas críticas em execução quando ativar ou desativar um ponto final de serviço para um serviço para uma sub-rede. Verifique também se as aplicações conseguem ligar-se automaticamente aos serviços do Azure após esta mudança do endereço IP.

  A mudança do endereço IP só afeta o tráfego de serviço da rede virtual. Não há nenhum impacto em qualquer outro tráfego endereçado de ou para os endereços IPv4 públicos atribuídos às suas máquinas virtuais. Nos serviços do Azure, se tiver regras de firewall existente que utilizem IPs públicos do Azure, estas regras deixam de funcionar com a mudança para os endereços privados da rede virtual.
- Com os pontos de extremidade de serviço, as entradas DNS dos serviços do Azure permanecem como estão hoje e continuam a ser resolvidas para endereços IP públicos atribuídos ao serviço do Azure.

- Grupos de segurança de rede (NSGs) com pontos finais de serviço:
  - Por padrão, o NSGs permite o tráfego de Internet de saída e também permite o tráfego de sua VNet para os serviços do Azure. Esse tráfego continua a funcionar com pontos de extremidade de serviço como está. 
  - Se você quiser negar todo o tráfego de Internet de saída e permitir apenas o tráfego para serviços específicos do Azure, poderá fazer isso usando as [marcas de serviço](security-overview.md#service-tags) em seu NSGs. Você pode especificar os serviços do Azure com suporte como destino em suas regras do NSG e o Azure também fornece a manutenção de endereços IP subjacentes a cada marca. Para obter mais informações, consulte [Etiquetas de Serviço do Azure para NSGs.](security-overview.md#service-tags) 

### <a name="scenarios"></a>Cenários

- **Redes virtuais múltiplas, ligadas ou ponto a ponto**: para proteger serviços do Azure em várias sub-redes numa rede virtual ou em várias redes virtuais, pode ativar pontos finais de serviço em cada uma destas sub-redes de forma independente e proteger os recursos de serviço do Azure em todas essas sub-redes.
- **Filtrando o tráfego de saída de uma rede virtual para os serviços do Azure**: se você quiser inspecionar ou filtrar o tráfego enviado a um serviço do Azure de uma rede virtual, poderá implantar um dispositivo de rede virtual na rede virtual. Em seguida, pode aplicar pontos finais de serviço à sub-rede onde está implementada a aplicação de rede virtual e proteger o recurso de serviço do Azure apenas nesta sub-rede. Esse cenário pode ser útil se você quiser usar a filtragem de dispositivo de virtualização de rede para restringir o acesso de serviço do Azure de sua rede virtual somente a recursos específicos do Azure. Para obter mais informações, veja [saída com aplicações de rede virtual](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Protegendo os recursos do Azure para serviços implantados diretamente em redes virtuais**: você pode implantar diretamente vários serviços do Azure em sub-redes específicas em uma rede virtual. Pode proteger recursos de serviço do Azure para sub-redes de [serviços geridos](virtual-network-for-azure-services.md) ao configurar um ponto final de serviço na sub-rede do serviço gerido.
- **Tráfego de disco de uma máquina virtual do Azure: o**tráfego de disco da máquina virtual para discos gerenciados e não gerenciados não é afetado por pontos de extremidade de serviço que encaminham alterações para o armazenamento do Azure. Esse tráfego inclui diskIO, bem como montagem e desmontagem. Você pode limitar o acesso REST a blobs de página para selecionar redes por meio de pontos de extremidade de serviço e [regras de rede de armazenamento do Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Registo e resolução de problemas

Depois de configurar os pontos de extremidade de serviço para um serviço específico, valide se a rota do ponto final do serviço está em vigor por: 
 
- Validação do endereço IP de origem de qualquer pedido de serviço no diagnóstico do serviço. Todos os novos pedidos com pontos finais de serviço mostram o endereço IP de origem para o pedido como o endereço IP privado da rede virtual atribuído ao cliente que efetua o pedido a partir da sua rede virtual. Sem o ponto final, o endereço é um endereço IP público do Azure.
- Visualização das rotas efetivas em qualquer interface de rede numa sub-rede. A rota para o serviço:
  - Mostra uma rota predefinida mais específica para endereçar intervalos de prefixo de cada serviço
  - Tem um nextHopType de *VirtualNetworkServiceEndpoint*
  - Indica que uma conexão mais direta com o serviço está em vigor em comparação com as rotas de túnel forçado

>[!NOTE]
> As rotas do ponto final de serviço substituem quaisquer rotas BGP ou UDR para a correspondência de prefixo de endereço de um serviço do Azure. Para obter mais informações, consulte [solução de problemas com rotas efetivas](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Aprovisionamento

Os pontos de extremidade de serviço podem ser configurados em redes virtuais independentemente de um usuário com acesso de gravação a uma rede virtual. Para proteger os recursos de serviço do Azure para uma VNet, o usuário deve ter permissão para *Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/Action* para as sub-redes adicionadas. As funções internas de administrador de serviços incluem essa permissão por padrão. Você pode modificar a permissão criando funções personalizadas.

Para obter mais informações sobre funções internas, consulte [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obter mais informações sobre como atribuir permissões específicas a funções personalizadas, consulte [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As redes virtuais e os recursos de serviço do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes. Se os recursos de serviço da rede virtual e do Azure pertencerem a subscrições diferentes, os recursos devem existir no mesmo inquilino do Active Directory (AD). 

## <a name="pricing-and-limits"></a>Preços e limites

Não há nenhum custo adicional para usar pontos de extremidade de serviço. O modelo de preços atual para os serviços do Azure (armazenamento do Azure, banco de dados SQL do Azure, etc.) aplica-se como está hoje.

Não há limite para o número total de pontos de extremidade de serviço em uma rede virtual.

Determinados serviços do Azure, como contas de armazenamento do Azure, podem impor limites no número de sub-redes usadas para proteger o recurso. Consulte a documentação para vários serviços na seção [próximas etapas](#next-steps) para obter detalhes.

## <a name="vnet-service-endpoint-policies"></a>Políticas de ponto de extremidade de serviço VNet 

As políticas de ponto de extremidade de serviço de VNet permitem filtrar o tráfego de rede virtual para os serviços do Azure. Esse filtro permite apenas recursos específicos do serviço do Azure em pontos de extremidade de serviço. Políticas de ponto final de serviço fornecem controlo de acesso granular para tráfego de rede virtual para serviços do Azure. Para obter mais informações, consulte [políticas de ponto de extremidade de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>FAQs

Para perguntas frequentes, consulte [ponto de extremidade de serviço de rede virtual FAQs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Passos seguintes

- [Configurar pontos de extremidade de serviço de rede virtual](tutorial-restrict-network-access-to-resources.md)
- [Proteger uma conta de armazenamento do Azure para uma rede virtual](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteger um banco de dados SQL do Azure para uma rede virtual](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteger um SQL Data Warehouse do Azure para uma rede virtual](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integração de serviço do Azure em redes virtuais](virtual-network-for-azure-services.md)
- [Políticas de ponto de extremidade de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

