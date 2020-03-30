---
title: Integrar o Azure Firewall no Balanceador de Carga Standard do Azure
description: Pode integrar uma Firewall Azure numa rede virtual com um Balancer de Carga Padrão Azure (público ou interno).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196729"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrar o Azure Firewall no Balanceador de Carga Standard do Azure

Pode integrar uma Firewall Azure numa rede virtual com um Balancer de Carga Padrão Azure (público ou interno). 

O design preferido é integrar um equilibrista de carga interna com a sua firewall Azure, uma vez que este é um design muito mais simples. Pode utilizar um equilibrador de carga pública se já tiver um implantado e quiser mantê-lo no lugar. No entanto, é preciso estar atento a um problema de encaminhamento assimétrico que pode quebrar a funcionalidade com o cenário de equilíbrio de carga pública.

Para mais informações sobre o Azure Load Balancer, consulte [o que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Equilibrador de carga pública

Com um equilibrista de carga pública, o equilibrador de carga é implantado com um endereço IP frontal público.

### <a name="asymmetric-routing"></a>Encaminhamento assimétrico

O encaminhamento assimétrico é onde um pacote toma um caminho para o destino e toma outro caminho ao regressar à fonte. Este problema ocorre quando uma subnet tem uma rota padrão indo para o endereço IP privado da firewall e você está usando um equilibrador de carga público. Neste caso, o tráfego de balanceadores de carga de entrada é recebido através do seu endereço IP público, mas a rota de retorno passa pelo endereço IP privado da firewall. Como a firewall é imponente, deixa cair o pacote de retorno porque a firewall não está ciente de uma sessão tão estabelecida.

### <a name="fix-the-routing-issue"></a>Corrija a questão do encaminhamento

Quando coloca uma Firewall Azure numa sub-rede, um passo é criar uma rota padrão para os pacotes de direção da sub-rede através do endereço IP privado da firewall localizado na AzureFirewallSubnet. Para mais informações, consulte [Tutorial: Desloque e configure a Firewall Azure utilizando o portal Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Quando introduzir a firewall no seu cenário de equilíbrio de carga, pretende que o seu tráfego de Internet entre no endereço IP público da sua firewall. A partir daí, a firewall aplica as suas regras de firewall e NATs os pacotes para o endereço IP público do seu equilibrador de carga. É aqui que o problema ocorre. Os pacotes chegam no endereço IP público da firewall, mas regressam à firewall através do endereço IP privado (utilizando a rota padrão).
Para evitar este problema, crie uma rota adicional de hospedagem para o endereço IP público da firewall. Os pacotes que vão para o endereço IP público da firewall são encaminhados através da Internet. Isto evita a rota padrão para o endereço IP privado da firewall.

![Encaminhamento assimétrico](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Exemplo de tabela de rotas

Por exemplo, as seguintes rotas são para uma firewall no endereço IP público 20.185.97.136 e endereço IP privado 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Tabela de rotas](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Exemplo de regra na NAT

No exemplo seguinte, uma regra NAT traduz o tráfego de RDP para a firewall em 20.185.97.136 para o equilibrista de carga em 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Regra na NAT](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Sondas do estado de funcionamento

Lembre-se, precisa de ter um serviço web a funcionar nos anfitriões na piscina de equilibradores de carga se utilizar sondas de saúde TCP para a porta 80, ou sondas HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Balanceador de carga interno

Com um equilibrante de carga interna, o equilibrador de carga é implantado com um endereço IP frontal privado.

Não há problema de encaminhamento assimétrico com este cenário. Os pacotes de entrada chegam ao endereço IP público da firewall, são traduzidos para o endereço IP privado do equilibrador de carga, e depois retornam ao endereço IP privado da firewall usando o mesmo caminho de retorno.

Assim, pode implementar este cenário semelhante ao cenário do equilíbrio de carga pública, mas sem a necessidade da rota de anfitriões de endereços IP públicos firewall.

## <a name="additional-security"></a>Segurança adicional

Para aumentar ainda mais a segurança do seu cenário equilibrado em carga, pode utilizar grupos de segurança de rede (NSGs).

Por exemplo, pode criar um NSG na subnet de backend onde estão localizadas as máquinas virtuais equilibradas em carga. Permitir o tráfego de entrada originário do endereço/porta IP firewall.

![Grupo de segurança de rede](media/integrate-lb/nsg-01.png)

Para obter mais informações sobre nsgs, consulte [grupos de segurança](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [implementar e configurar uma Firewall Azure](tutorial-firewall-deploy-portal.md).