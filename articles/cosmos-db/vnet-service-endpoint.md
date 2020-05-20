---
title: Acesso seguro às contas do Azure Cosmos DB utilizando o ponto final do serviço de rede virtual
description: Este documento descreve sobre a rede virtual e o controlo de acesso à sub-rede para uma conta Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d264ead87e7fa638830bf25fdb07983b164334b7
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698661"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Aceder ao Azure Cosmos DB a partir de redes virtuais (VNet)

Pode configurar a conta Azure Cosmos para permitir o acesso apenas a partir de uma subnet específica da rede virtual (VNet). Ao permitir que o [ponto final do Serviço](../virtual-network/virtual-network-service-endpoints-overview.md) aceda ao Azure Cosmos DB na subnet dentro de uma rede virtual, o tráfego dessa subrede é enviado para o Azure Cosmos DB com a identidade da subnet e da Rede Virtual. Uma vez ativado o ponto final do serviço Azure Cosmos DB, pode limitar o acesso à subrede adicionando-o à sua conta Azure Cosmos.

Por predefinição, uma conta Azure Cosmos é acessível a partir de qualquer fonte se o pedido for acompanhado por um sinal de autorização válido. Quando adicionar uma ou mais subredes dentro de VNets, apenas os pedidos originários dessas subredes receberão uma resposta válida. Os pedidos provenientes de qualquer outra fonte receberão uma resposta 403 (Proibida). 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Aqui ficam algumas perguntas frequentes sobre a configuração do acesso a partir de redes virtuais:

### <a name="are-notebooks-and-mongo-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Os Cadernos e a Mongo Shell são atualmente compatíveis com contas ativadas pela Rede Virtual?

Neste momento, a integração da [concha de Mongo no Cosmos DB Data Explorer](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) e no serviço [Jupyter Notebooks](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-jupyter-notebooks) não são suportadas com acesso VNET. Isto encontra-se atualmente em desenvolvimento ativo.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Posso especificar tanto a política de controlo do serviço de rede virtual como a política de controlo de acesso IP numa conta Azure Cosmos? 

Pode ativar tanto o ponto final do serviço de rede virtual como uma política de controlo de acesso IP (aka firewall) na sua conta Azure Cosmos. Estas duas funcionalidades são complementares e garantem coletivamente o isolamento e a segurança da sua conta Azure Cosmos. A utilização de firewall IP garante que os IPs estáticos podem aceder à sua conta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Como posso limitar o acesso à subrede dentro de uma rede virtual? 

Há dois passos necessários para limitar o acesso à conta Azure Cosmos a partir de uma subnet. Em primeiro lugar, permite que o tráfego da subnet leve a sua subnet e identidade de rede virtual para o Azure Cosmos DB. Isto é feito permitindo o ponto final de serviço para o Azure Cosmos DB na subnet. Em seguida, é adicionar uma regra na conta Azure Cosmos especificando esta subnet como uma fonte a partir da qual a conta pode ser acedida.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Os ACLs da rede virtual e a Firewall IP rejeitarão pedidos ou ligações? 

Quando a firewall IP ou as regras de acesso à rede virtual são adicionadas, apenas os pedidos de fontes permitidas obtêm respostas válidas. Outros pedidos são rejeitados com um 403 (Proibido). É importante distinguir a firewall da conta Azure Cosmos de uma firewall de nível de ligação. A fonte ainda pode ligar-se ao serviço e as ligações em si não são rejeitadas.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Os meus pedidos começaram a ser bloqueados quando permiti o ponto final de serviço da Azure Cosmos DB na subnet. O que aconteceu?

Uma vez que o ponto final de serviço para o Azure Cosmos DB é ativado numa subnet, a fonte do tráfego que chega à conta muda de IP público para rede virtual e subnet. Se a sua conta Azure Cosmos tiver apenas firewall baseada em IP, o tráfego da subnet ativado pelo serviço deixaria de corresponder às regras de firewall IP e, portanto, seria rejeitado. Recorra aos degraus para migrar perfeitamente da firewall baseada em IP para o controlo de acesso virtual baseado em rede.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>São necessárias permissões RBAC adicionais para contas Azure Cosmos com pontos finais de serviço VNET?

Depois de adicionar os pontos finais do serviço VNet a uma conta Azure Cosmos, para efazer quaisquer alterações nas definições da conta, precisa de ter acesso à `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` ação para todos os VNETs configurados na sua conta Azure Cosmos. Esta permissão é necessária porque o processo de autorização valida o acesso a recursos (como base de dados e recursos de rede virtual) antes de avaliar quaisquer propriedades.
 
A autorização valida a permissão para a ação de recursos VNet mesmo que o utilizador não especifique os AcLs VNET utilizando o Azure CLI. Atualmente, o plano de controlo da conta Azure Cosmos suporta a definição do estado completo da conta Azure Cosmos. Um dos parâmetros para as chamadas do avião de controlo `virtualNetworkRules` é. Se este parâmetro não for especificado, o Azure CLI faz uma chamada de base de dados para obter a recuperação do valor e utiliza este valor na chamada de `virtualNetworkRules` atualização.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>As redes virtuais também têm acesso à conta Azure Cosmos? 
Apenas a rede virtual e as suas subredes adicionadas à conta Azure Cosmos têm acesso. Os seus VNets peered não podem aceder à conta até que as subredes dentro das redes virtuais peered sejam adicionadas à conta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Qual é o número máximo de subredes autorizadas a aceder a uma única conta cosmos? 
Atualmente, pode ter no máximo 256 subnets permitidas para uma conta Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Posso permitir o acesso da VPN e da Rota expresso? 
Para aceder à conta Azure Cosmos sobre a rota Express a partir de instalações, você precisaria de ativar o peering da Microsoft. Uma vez que coloque regras de acesso à firewall IP ou à rede virtual, pode adicionar os endereços IP públicos utilizados para a Microsoft a espreitar na sua conta Azure Cosmos ip firewall para permitir no acesso aos serviços de instalações o acesso à conta Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Preciso atualizar as regras dos Grupos de Segurança da Rede (NSG) ? 
As regras do NSG são usadas para limitar a conectividade de e para uma subnet com rede virtual. Quando adiciona o ponto final de serviço para o Azure Cosmos DB à subnet, não há necessidade de abrir a conectividade de saída no NSG para a sua conta Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Os pontos finais do serviço estão disponíveis para todos os VNets?
Não, apenas as redes virtuais do Gestor de Recursos Azure podem ter o ponto final do serviço ativado. Redes virtuais clássicas não suportam pontos finais de serviço.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Posso "Aceitar ligações de dentro de datacenters públicos do Azure" quando o acesso final do serviço está ativado para o Azure Cosmos DB?  
Isto só é necessário quando pretende que a sua conta Azure Cosmos DB seja acedida por outros serviços de primeira festa do Azure, como a fábrica de Dados Azure, a Pesquisa Cognitiva Azure ou qualquer serviço que seja implantado na região do Azure.


## <a name="next-steps"></a>Passos seguintes

* [Como limitar o acesso à conta Azure Cosmos a redes virtuais](how-to-configure-vnet-service-endpoint.md)
* [Como configurar firewall IP para a sua conta Azure Cosmos](how-to-configure-firewall.md)

