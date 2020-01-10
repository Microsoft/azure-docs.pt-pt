---
title: Proteger o acesso a contas de Azure Cosmos DB usando o ponto de extremidade de serviço de rede virtual
description: Este documento descreve sobre a rede virtual e o controle de acesso à sub-rede para uma conta do Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: af1032de9aabac45ad7a86cfe1f36ed2c04c0f71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444624"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Acessar Azure Cosmos DB de redes virtuais (VNet)

Você pode configurar a conta do Azure Cosmos para permitir o acesso somente de uma sub-rede específica da rede virtual (VNet). Ao habilitar o [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para acessar Azure Cosmos DB na sub-rede em uma rede virtual, o tráfego dessa sub-rede é enviado para Azure Cosmos DB com a identidade da sub-rede e da rede virtual. Depois que o ponto de extremidade de serviço do Azure Cosmos DB estiver habilitado, você poderá limitar o acesso à sub-rede adicionando-a à sua conta do Azure Cosmos.

Por padrão, uma conta do Azure Cosmos pode ser acessada de qualquer origem se a solicitação estiver acompanhada por um token de autorização válido. Quando você adiciona uma ou mais sub-redes em VNets, somente as solicitações provenientes dessas sub-redes receberão uma resposta válida. As solicitações provenientes de qualquer outra fonte receberão uma resposta 403 (proibido). 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Aqui estão algumas perguntas frequentes sobre a configuração de acesso de redes virtuais:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Posso especificar o ponto de extremidade do serviço de rede virtual e a política de controle de acesso de IP em uma conta do Azure Cosmos? 

Você pode habilitar o ponto de extremidade de serviço de rede virtual e uma política de controle de acesso de IP (também conhecido como firewall) em sua conta do Azure Cosmos. Esses dois recursos são complementares e, coletivamente, garantem o isolamento e a segurança da sua conta do Azure Cosmos. Usar o firewall de IP garante que os IPs estáticos possam acessar sua conta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Como fazer limitar o acesso à sub-rede em uma rede virtual? 

Há duas etapas necessárias para limitar o acesso à conta do Azure Cosmos de uma sub-rede. Primeiro, você permite que o tráfego da sub-rede carregue sua sub-rede e a identidade da rede virtual para Azure Cosmos DB. Isso é feito habilitando o ponto de extremidade de serviço para Azure Cosmos DB na sub-rede. A seguir está a adição de uma regra na conta do Azure cosmos que especifica essa sub-rede como uma fonte da qual a conta pode ser acessada.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>As ACLs de rede virtual e o firewall IP rejeitam solicitações ou conexões? 

Quando as regras de acesso de rede virtual ou firewall de IP são adicionadas, somente as solicitações de fontes permitidas recebem respostas válidas. Outras solicitações são rejeitadas com um 403 (proibido). É importante distinguir o firewall da conta do Azure Cosmos de um firewall de nível de conexão. A origem ainda pode se conectar ao serviço e as próprias conexões não são rejeitadas.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Minhas solicitações começaram a ser bloqueadas quando eu habilitei o ponto de extremidade de serviço para Azure Cosmos DB na sub-rede. O que aconteceu?

Depois que o ponto de extremidade de serviço para Azure Cosmos DB estiver habilitado em uma sub-rede, a origem do tráfego que alcança a conta alternará do IP público para a rede virtual e a sub-rede. Se sua conta do Azure Cosmos tiver somente firewall baseado em IP, o tráfego da sub-rede habilitada para serviço não corresponderá mais às regras de firewall de IP e, portanto, será rejeitado. Siga as etapas para migrar diretamente do firewall baseado em IP para o controle de acesso baseado em rede virtual.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>As permissões RBAC adicionais são necessárias para contas do Azure cosmos com pontos de extremidade de serviço de VNET?

Depois de adicionar os pontos de extremidade do serviço de VNet a uma conta do Azure Cosmos, para fazer alterações nas configurações da conta, você precisa ter acesso à ação de `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` para todos os VNETs configurados em sua conta do cosmos do Azure. Essa permissão é necessária porque o processo de autorização valida o acesso a recursos (como recursos de rede virtual e de banco de dados) antes de avaliar qualquer propriedade.
 
A autorização valida a permissão para a ação de recurso de VNet, mesmo que o usuário não especifique as ACLs de VNET usando CLI do Azure. Atualmente, o plano de controle da conta do Azure Cosmos dá suporte à definição do estado completo da conta do Azure Cosmos. Um dos parâmetros para as chamadas do plano de controle é `virtualNetworkRules`. Se esse parâmetro não for especificado, o CLI do Azure fará uma chamada Get Database para recuperar o `virtualNetworkRules` e usará esse valor na chamada Update.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>As redes virtuais emparelhadas também têm acesso à conta do Azure Cosmos? 
Somente a rede virtual e suas sub-redes adicionadas à conta do Azure Cosmos têm acesso. Seus VNets emparelhados não podem acessar a conta até que as sub-redes em redes virtuais emparelhadas sejam adicionadas à conta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Qual é o número máximo de sub-redes permitidas para acessar uma única conta do cosmos? 
No momento, você pode ter no máximo 64 sub-redes permitidas para uma conta do Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Posso habilitar o acesso de VPN e de rota expressa? 
Para acessar a conta do Azure Cosmos sobre a rota expressa do local, você precisaria habilitar o emparelhamento da Microsoft. Depois de colocar as regras de acesso de rede virtual ou firewall IP, você pode adicionar os endereços IP públicos usados para o emparelhamento da Microsoft em seu firewall de IP da conta do Azure Cosmos para permitir o acesso de serviços locais à conta do Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>É necessário atualizar as regras de NSG (grupos de segurança de rede)? 
As regras NSG são usadas para limitar a conectividade de e para uma sub-rede com uma rede virtual. Quando você adiciona um ponto de extremidade de serviço para Azure Cosmos DB à sub-rede, não é necessário abrir a conectividade de saída em NSG para sua conta do cosmos do Azure. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Os pontos de extremidade de serviço estão disponíveis para todos os VNets?
Não, somente Azure Resource Manager redes virtuais podem ter o ponto de extremidade de serviço habilitado. As redes virtuais clássicas não dão suporte a pontos de extremidade de serviço.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Posso "aceitar conexões de data centers públicos do Azure" quando o acesso ao ponto de extremidade de serviço estiver habilitado para Azure Cosmos DB?  
Isso é necessário apenas quando você deseja que sua conta de Azure Cosmos DB seja acessada por outros serviços de terceiros do Azure, como o Azure data Factory, o Azure Pesquisa Cognitiva ou qualquer serviço implantado em determinada região do Azure.


## <a name="next-steps"></a>Passos seguintes

* [Como limitar o acesso de conta do Azure Cosmos a sub-redes em redes virtuais](how-to-configure-vnet-service-endpoint.md)
* [Como configurar o firewall IP para sua conta do Azure Cosmos](how-to-configure-firewall.md)

