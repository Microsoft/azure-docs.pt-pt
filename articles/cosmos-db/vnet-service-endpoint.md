---
title: Proteger o acesso a uma conta do Azure Cosmos DB utilizando o ponto final de serviço de rede Virtual do Azure
description: Este documento descreve sobre a rede virtual e o controlo de acesso de sub-rede para uma conta do Cosmos do Azure.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4e609bf6d96fa9cc3f2dc179157ecd27178608f9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860368"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Acesso do Azure Cosmos DB a partir de redes virtuais (VNet)

Pode configurar a conta do Cosmos do Azure para permitir o acesso apenas a partir de uma sub-rede específica da rede virtual (VNet). Ativando [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para aceder ao Azure Cosmos DB na sub-rede numa rede virtual, o tráfego a partir dessa sub-rede é enviado para o Azure Cosmos DB com a identidade do sub-rede e da rede Virtual. Assim que o ponto de extremidade de serviço do Azure Cosmos DB está ativado, pode limitar o acesso à sub-rede, adicionando-o à sua conta do Cosmos do Azure.

Por predefinição, uma conta do Cosmos do Azure é acessível a partir de qualquer origem se o pedido seja acompanhado por um token de autorização válida. Quando adiciona um ou mais sub-redes nas VNets, apenas os pedidos com origem dessas sub-redes irão obter uma resposta válida. Pedidos com origem em qualquer outra origem irão receber uma resposta (proibido) 403. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Seguem-se algumas perguntas mais frequentes sobre como configurar o acesso a partir de redes virtuais:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Posso especificar o ponto final de serviço de rede virtual e política de controlo de acesso IP numa conta do Cosmos do Azure? 

Pode ativar o ponto de extremidade do serviço de rede virtual e uma política de controlo de acesso IP (também conhecido como firewall) na sua conta do Cosmos do Azure. Esses dois recursos são complementares e, coletivamente, certifique-se de isolamento e de segurança da sua conta do Cosmos do Azure. Utilizar um IP firewall garante que IPs estáticos podem aceder à sua conta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Como posso limitar o acesso à sub-rede numa rede virtual? 

Existem dois passos necessários para limitar o acesso à conta do Cosmos do Azure a partir de uma sub-rede. Em primeiro lugar, que permite o tráfego da sub-rede para executar a sua sub-rede e a identidade de rede virtual para o Azure Cosmos DB. Isso é feito ao ativar o ponto final de serviço para o Azure Cosmos DB na sub-rede. Em seguida é adicionar uma regra na conta do Azure Cosmos especificando esta sub-rede como uma origem a partir do qual a conta pode ser acessada.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>ACLs de rede virtual e Firewall do IP irão rejeitar pedidos ou ligações? 

Quando acedem a firewall do IP ou de rede virtual regras são adicionadas, apenas os pedidos de respostas válidas de get origens permitidas. Outros pedidos são rejeitados com um erro 403 (proibido). É importante distinguir firewall da conta do Cosmos do Azure a partir de uma firewall ao nível de ligação. A origem ainda pode ligar ao serviço e as ligações propriamente ditas não são rejeitadas.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Os meus pedidos começou a ser bloqueado quando eu habilitei o ponto final de serviço para o Azure Cosmos DB na sub-rede. O que aconteceu?

Assim que o ponto final de serviço para o Azure Cosmos DB está ativado numa sub-rede, muda a origem do tráfego atingir a conta do IP público para a rede virtual e sub-rede. Se a sua conta do Cosmos do Azure tem firewall baseado em IP apenas, o tráfego da sub-rede de serviço ativado já não corresponderia as regras de firewall do IP e, portanto, ser rejeitado. Abordar os passos para migrar forma totalmente integrada de firewall baseado em IP para o controlo de acesso baseados na rede virtual.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>As redes virtuais em modo de peering têm também acesso à conta do Cosmos do Azure? 
Apenas a rede virtual e a suas sub-redes adicionados à conta do Cosmos do Azure têm acesso. As VNets em modo de peering não é possível aceder à conta até que as sub-redes dentro de redes virtuais em modo de peering são adicionadas à conta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>O que é o número máximo de sub-redes permissão para aceder a uma única conta do Cosmos? 
Atualmente, pode ter no máximo 64 sub-redes permitidas para uma conta do Cosmos do Azure.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Posso ativar o acesso a partir de VPN e o Express Route? 
Para aceder à conta do Cosmos do Azure através de Express route no local, terá de ativar o peering da Microsoft. Depois de colocar o firewall do IP ou regras de acesso de rede virtual, pode adicionar os endereços IP públicos utilizados para peering da Microsoft na sua firewall IP de conta do Cosmos do Azure para permitir o acesso de serviços no local para a conta do Cosmos do Azure. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>É necessário atualizar as regras de grupos de segurança de rede (NSG)? 
As regras do NSG são utilizadas para limitar a conectividade de e para uma sub-rede com a rede virtual. Quando adiciona o ponto final de serviço para o Azure Cosmos DB para a sub-rede, não é necessário para abrir a conectividade de saída no NSG para a sua conta do Cosmos do Azure. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Pontos finais de serviço estão disponíveis para todas as VNets?
Não, apenas o Azure Resource Manager redes virtuais podem ter o ponto final de serviço ativado. Redes virtuais clássicas não suportam os pontos finais de serviço.


## <a name="next-steps"></a>Passos Seguintes

* [Como limitar o acesso de conta do Cosmos do Azure para sub-redes dentro de redes virtuais](how-to-configure-vnet-service-endpoint.md)
* [Como configurar a firewall do IP para a sua conta do Cosmos do Azure](how-to-configure-firewall.md)

