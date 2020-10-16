---
title: Acesso seguro às contas DB da Azure Cosmos utilizando o ponto final do serviço de rede virtual
description: Este documento descreve sobre rede virtual e controlo de acesso a sub-redes para uma conta Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 10f2b44d00361885778a523109a8fcb05dabe9dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91574352"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Aceder ao Azure Cosmos DB a partir de redes virtuais (VNet)

Pode configurar a conta Azure Cosmos para permitir o acesso apenas a partir de uma sub-rede específica de rede virtual (VNet). Ao permitir o [acesso ao](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Cosmos DB na sub-rede dentro de uma rede virtual, o tráfego dessa sub-rede é enviado para a Azure Cosmos DB com a identidade da sub-rede e da Rede Virtual. Uma vez ativado o ponto final do serviço Azure Cosmos DB, pode limitar o acesso à sub-rede adicionando-o à sua conta Azure Cosmos.

Por padrão, uma conta Azure Cosmos está acessível a partir de qualquer fonte se o pedido for acompanhado de um token de autorização válido. Quando adicionar uma ou mais sub-redes dentro de VNets, apenas os pedidos originários dessas sub-redes terão uma resposta válida. Os pedidos originários de qualquer outra fonte receberão uma resposta 403 (Proibida). 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Aqui estão algumas perguntas frequentes sobre a configuração do acesso a partir de redes virtuais:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Os Cadernos e a Mongo/Cassandra Shell são atualmente compatíveis com contas de Rede Virtual ativadas?

De momento, as integrações da [concha de Mongo](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) e [da Cassandra](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) no Cosmos DB Data Explorer, e o [serviço Jupyter Notebooks,](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-jupyter-notebooks)não são suportados com acesso VNET. Isto encontra-se atualmente em desenvolvimento ativo.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Posso especificar tanto o ponto final do serviço de rede virtual como a política de controlo de acesso IP numa conta Azure Cosmos? 

Pode ativar tanto o ponto final do serviço de rede virtual como uma política de controlo de acesso IP (aka firewall) na sua conta Azure Cosmos. Estas duas características são complementares e coletivamente garantem o isolamento e a segurança da sua conta Azure Cosmos. A utilização de firewall IP garante que os IPs estáticos podem aceder à sua conta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Como limito o acesso à sub-rede dentro de uma rede virtual? 

Há dois passos necessários para limitar o acesso à conta Azure Cosmos a partir de uma sub-rede. Em primeiro lugar, permite que o tráfego da sub-rede leve a sua sub-rede e identidade de rede virtual para a Azure Cosmos DB. Isto é feito permitindo o ponto final de serviço para Azure Cosmos DB na sub-rede. Em seguida, é adicionar uma regra na conta Azure Cosmos especificando esta sub-rede como uma fonte a partir da qual a conta pode ser acedida.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Os ACLs de rede virtual e o IP Firewall rejeitarão pedidos ou ligações? 

Quando são adicionadas regras de firewall IP ou de acesso à rede virtual, apenas os pedidos de fontes permitidas obtêm respostas válidas. Outros pedidos são rejeitados com um 403 (Proibido). É importante distinguir a firewall da conta Azure Cosmos de uma firewall de nível de ligação. A fonte ainda pode ligar-se ao serviço e as próprias ligações não são rejeitadas.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Os meus pedidos começaram a ser bloqueados quando ativei o ponto final do serviço para a Azure Cosmos DB na sub-rede. O que aconteceu?

Uma vez que o ponto final de serviço para Azure Cosmos DB é ativado numa sub-rede, a fonte do tráfego que alcança a conta muda de IP público para rede virtual e sub-rede. Se a sua conta Azure Cosmos tiver apenas firewall baseada em IP, o tráfego da sub-rede ativada pelo serviço deixaria de corresponder às regras de firewall IP e, portanto, seria rejeitado. Revindo os passos para migrar perfeitamente de firewall baseada em IP para o controlo de acesso baseado em rede virtual.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>São necessárias permissões adicionais de RBAC para contas Azure Cosmos com pontos finais de serviço VNET?

Depois de adicionar os pontos finais do serviço VNet a uma conta Azure Cosmos, para efetivar quaisquer alterações nas definições da conta, precisa de ter acesso à `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` ação para todos os VNETs configurados na sua conta Azure Cosmos. Esta permissão é necessária porque o processo de autorização valida o acesso a recursos (como base de dados e recursos de rede virtual) antes de avaliar quaisquer propriedades.
 
A autorização valida a permissão para a ação de recursos VNet mesmo que o utilizador não especifique os ACLs VNET utilizando o Azure CLI. Atualmente, o avião de controlo da conta Azure Cosmos suporta a definição do estado completo da conta Azure Cosmos. Um dos parâmetros para as chamadas do avião de controlo `virtualNetworkRules` é. Se este parâmetro não for especificado, o CLI do Azure faz uma chamada de base de dados para recuperar o `virtualNetworkRules` valor e utiliza este valor na chamada de atualização.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>As redes virtuais espreitadas também têm acesso à conta Azure Cosmos? 
Apenas a rede virtual e as suas sub-redes adicionadas à conta Azure Cosmos têm acesso. Os seus VNets espreitados não podem aceder à conta até que as sub-redes dentro das redes virtuais sejam adicionadas à conta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Qual é o número máximo de sub-redes autorizados a aceder a uma única conta Cosmos? 
Atualmente, você pode ter no máximo 256 sub-redes permitidas para uma conta Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Posso permitir o acesso a partir da VPN e da Rota Expressa? 
Para aceder à conta Azure Cosmos sobre a rota Express a partir das instalações, você precisaria de ativar o observamento da Microsoft. Assim que colocar regras de firewall IP ou acesso à rede virtual, pode adicionar os endereços IP públicos utilizados para a Microsoft a espreitar na sua conta IP da Azure Cosmos para permitir o acesso dos serviços de instalações à conta Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Preciso de atualizar as regras dos Grupos de Segurança da Rede (NSG)? 
As regras NSG são usadas para limitar a conectividade de e para uma sub-rede com rede virtual. Quando adicionas o ponto final de serviço para a Azure Cosmos DB à sub-rede, não há necessidade de abrir a conectividade de saída em NSG para a sua conta Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Os pontos finais de serviço estão disponíveis para todos os VNets?
Não, apenas as redes virtuais do Azure Resource Manager podem ter o ponto final de serviço ativado. As redes virtuais clássicas não suportam pontos finais de serviço.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Posso "Aceitar ligações de dentro dos datacenters públicos da Azure" quando o acesso ao ponto final do serviço estiver ativado para a Azure Cosmos DB?  
Isto só é necessário quando pretende que a sua conta DB Azure Cosmos seja acedida por outros serviços de primeira festa da Azure, como a fábrica de Dados Azure, a Azure Cognitive Search ou qualquer serviço que seja implantado na região de Azure.


## <a name="next-steps"></a>Passos seguintes

* [Como limitar o acesso da conta Azure Cosmos a sub-redes dentro de redes virtuais](how-to-configure-vnet-service-endpoint.md)
* [Como configurar firewall IP para a sua conta Azure Cosmos](how-to-configure-firewall.md)

