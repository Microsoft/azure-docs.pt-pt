---
title: Integrar o Azure Firewall no Balanceador de Carga Standard do Azure
description: Pode integrar um Azure Firewall numa rede virtual com um Azure Standard Load Balancer (público ou interno).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: 3bde4c11e9dc34be13efb25864fe75054d22bddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91363108"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrar o Azure Firewall no Balanceador de Carga Standard do Azure

Pode integrar um Azure Firewall numa rede virtual com um Azure Standard Load Balancer (público ou interno). 

O design preferencial é integrar um balanceador de carga interno com o Azure Firewall, uma vez que este é um design muito mais simples. Poderá utilizar um balanceador de carga público se já tiver um implementado e quiser mantê-lo. No entanto, precisa de estar atento a um problema de encaminhamento assimétrico que pode quebrar a funcionalidade no cenário do balanceador de carga público.

Para obter mais informações sobre o Azure Load Balancer, veja [o que é o Equilibr de Carga Azure?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Balanceador de carga público

Com um equilibrador de carga público, o equilibrador de carga é implantado com um endereço IP frontal público.

### <a name="asymmetric-routing"></a>Encaminhamento assimétrico

O encaminhamento assimétrico é onde um pacote toma um caminho para o destino e toma outro caminho quando regressa à fonte. Este problema ocorre quando uma sub-rede tem uma rota padrão indo para o endereço IP privado da firewall e você está usando um equilibrador de carga pública. Neste caso, o tráfego do balançador de carga de entrada é recebido através do seu endereço IP público, mas a trajetória de retorno passa pelo endereço IP privado da firewall. Uma vez que a firewall é imponente, deixa cair o pacote de retorno porque a firewall não está ciente de uma sessão tão estabelecida.

### <a name="fix-the-routing-issue"></a>Corrigir o problema do encaminhamento

Quando coloca uma Firewall Azure numa sub-rede, um passo é criar uma rota padrão para os pacotes de direcionamento da sub-rede através do endereço IP privado da firewall localizado na AzureFirewallSubnet. Para obter mais informações, consulte [Tutorial: Implemente e configuure Azure Firewall utilizando o portal Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Quando introduzir a firewall no seu cenário de balanceador de carga, pretende que o seu tráfego de Internet entre através do endereço IP público da sua firewall. A partir daí, a firewall aplica as suas regras de firewall e OS NATs os pacotes para o endereço IP público do seu balancer de carga. É aqui que o problema ocorre. Os pacotes chegam no endereço IP público da firewall, mas regressam à firewall através do endereço IP privado (utilizando a rota predefinido).
Para evitar este problema, crie uma rota de anfitrião adicional para o endereço IP público da firewall. Os pacotes que vão para o endereço IP público da firewall são encaminhados através da Internet. Isto evita tomar a rota padrão para o endereço IP privado da firewall.

![Encaminhamento assimétrico](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Exemplo de tabela de rota

Por exemplo, as seguintes rotas são para uma firewall no endereço IP público 20.185.97.136, e endereço IP privado 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Tabela de rotas](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Exemplo de regra nat

No exemplo seguinte, uma regra NAT traduz o tráfego RDP para a firewall em 20.185.97.136 para o balançador de carga em 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Regra NAT](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Sondas do estado de funcionamento

Lembre-se, precisa de ter um serviço web a funcionar nos anfitriões na piscina do balanceador de carga se utilizar sondas de saúde TCP para a porta 80 ou sondas HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Balanceador de carga interno

Com um equilibrador de carga interno, o balançador de carga é implantado com um endereço IP frontal privado.

Não há problema de encaminhamento assimétrico com este cenário. Os pacotes de entrada chegam ao endereço IP público da firewall, são traduzidos para o endereço IP privado do balançador de carga e, em seguida, retornam para o endereço IP privado da firewall usando a mesma trajetória de retorno.

Assim, você pode implementar este cenário semelhante ao cenário do balanceador de carga pública, mas sem a necessidade da rota de anfitrião de endereço IP público firewall.

>[!NOTE]
>As máquinas virtuais no pool de backend não terão conectividade de internet de saída com esta configuração. </br> Para obter mais informações sobre o fornecimento de conectividade de saída consulte: </br> **[Ligações de saída no Azure](../load-balancer/load-balancer-outbound-connections.md)**</br> Opções para fornecer conectividade: </br> **[Configuração do balanceador de carga só de saída](../load-balancer/egress-only.md)** </br> [**O que é Virtual Network NAT?**](../virtual-network/nat-overview.md)


## <a name="additional-security"></a>Segurança adicional

Para aumentar ainda mais a segurança do seu cenário equilibrado de carga, pode utilizar grupos de segurança de rede (NSGs).

Por exemplo, pode criar um NSG na sub-rede de backend onde estão localizadas as máquinas virtuais equilibradas de carga. Permitir a entrada de tráfego originário do endereço IP/porta IP da firewall.

![Grupo de segurança de rede](media/integrate-lb/nsg-01.png)

Para obter mais informações sobre os NSGs, consulte [os grupos de segurança.](../virtual-network/security-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [implantar e configurar um Azure Firewall](tutorial-firewall-deploy-portal.md).