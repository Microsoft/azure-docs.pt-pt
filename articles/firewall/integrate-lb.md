---
title: Integrar o Azure Firewall no Balanceador de Carga Standard do Azure
description: Você pode integrar um firewall do Azure em uma rede virtual com um Standard Load Balancer do Azure (público ou interno).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 91f34d06532b2d7f56d293df40939212a4f3d68c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167070"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrar o Azure Firewall no Balanceador de Carga Standard do Azure

Você pode integrar um firewall do Azure em uma rede virtual com um Standard Load Balancer do Azure (público ou interno). 

O design preferencial é integrar um balanceador de carga interno com o Firewall do Azure, pois esse é um design muito mais simples. Você pode usar um balanceador de carga público se já tiver um implantado e desejar mantê-lo em vigor. No entanto, você precisa estar ciente de um problema de roteamento assimétrico que pode interromper a funcionalidade com o cenário de balanceador de carga público.

Para obter mais informações sobre Azure Load Balancer, consulte [o que é Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Balanceador de carga público

Com um balanceador de carga público, o balanceador de carga é implantado com um endereço IP de front-end público.

### <a name="asymmetric-routing"></a>Encaminhamento assimétrico

O roteamento assimétrico é onde um pacote usa um caminho para o destino e usa outro caminho ao retornar à origem. Esse problema ocorre quando uma sub-rede tem uma rota padrão indo para o endereço IP privado do firewall e você está usando um balanceador de carga público. Nesse caso, o tráfego do balanceador de carga de entrada é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall tem monitoração de estado, ele descarta o pacote de retorno porque o firewall não está ciente dessa sessão estabelecida.

### <a name="fix-the-routing-issue"></a>Corrigir o problema de roteamento

Quando você implanta um firewall do Azure em uma sub-rede, uma etapa é criar uma rota padrão para a sub-rede direcionando pacotes por meio do endereço IP privado do firewall localizado no AzureFirewallSubnet. Para obter mais informações, consulte [tutorial: implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Ao introduzir o firewall em seu cenário de balanceador de carga, você deseja que seu tráfego de Internet chegue por meio do endereço IP público do firewall. A partir daí, o firewall aplica suas regras de firewall e NATs os pacotes para o endereço IP público do balanceador de carga. É aí que o problema ocorre. Os pacotes chegam no endereço IP público do firewall, mas retornam ao firewall por meio do endereço IP privado (usando a rota padrão).
Para evitar esse problema, crie uma rota de host adicional para o endereço IP público do firewall. Os pacotes que vão para o endereço IP público do firewall são roteados pela Internet. Isso evita pegar a rota padrão para o endereço IP privado do firewall.

![Encaminhamento assimétrico](media/integrate-lb/Firewall-LB-asymmetric.png)

Por exemplo, as rotas a seguir são para um firewall no endereço IP público 13.86.122.41 e o endereço IP privado 10.3.1.4.

![Tabela de rota](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Balanceador de carga interno

Com um balanceador de carga interno, o balanceador de carga é implantado com um endereço IP de front-end privado.

Não há nenhum problema de roteamento assimétrico com esse cenário. Os pacotes de entrada chegam ao endereço IP público do firewall, são convertidos no endereço IP privado do balanceador de carga e, em seguida, retorna ao endereço IP privado do firewall usando o mesmo caminho de retorno.

Portanto, você pode implantar esse cenário de forma semelhante ao cenário do Load Balancer público, mas sem a necessidade da rota de host de endereço IP público do firewall.

## <a name="additional-security"></a>Segurança adicional

Para aprimorar ainda mais a segurança do cenário de balanceamento de carga, você pode usar NSGs (grupos de segurança de rede).

Por exemplo, você pode criar um NSG na sub-rede de back-end onde as máquinas virtuais com balanceamento de carga estão localizadas. Permitir tráfego de entrada proveniente da porta/endereço IP do firewall.

Para obter mais informações sobre NSGs, consulte [grupos de segurança](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md).