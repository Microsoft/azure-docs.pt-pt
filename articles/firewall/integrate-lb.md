---
title: Integrar o Firewall do Azure com o Balanceador de carga Standard do Azure
description: Saiba como integrar o Firewall do Azure com o Balanceador de carga Standard do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 7ee92a7508918635849caafab4632bbba81ee628
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805249"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrar o Firewall do Azure com o Balanceador de carga Standard do Azure

Pode integrar um Firewall do Azure uma rede virtual do Azure Standard Balanceador de carga com (públicos ou internos). 

A conceção preferida é integrar o Balanceador de carga interno com a firewall do Azure, como se trata de um design muito mais simples. Pode utilizar um balanceador de carga público se já tiver um implementado e pretender mantê-lo no local. No entanto, precisa estar atento um problema de encaminhamento assimétrico que pode interromper a funcionalidade com o cenário de Balanceador de carga público.

Para obter mais informações sobre o Balanceador de carga do Azure, consulte [o que é o Balanceador de carga do Azure?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Balanceador de carga público

Com um balanceador de carga público, o Balanceador de carga é implementado com um endereço IP público de front-end.

### <a name="asymmetric-routing"></a>Encaminhamento assimétrico

Encaminhamento assimétrico é onde um pacote usa um caminho para o destino e leva a outro caminho ao retornar para a origem. Este problema ocorre quando uma sub-rede tiver uma rota predefinida vai para o endereço IP privado da firewall e estiver a utilizar um balanceador de carga público. Neste caso, o tráfego do Balanceador de carga entrada é recebido através do respetivo endereço IP público, mas o caminho de retorno passa por endereço IP privado da firewall. Uma vez que a firewall está com monitoração de estado, remove o pacote de devolver uma vez que a firewall não está ciente de uma sessão estabelecida.

### <a name="fix-the-routing-issue"></a>Corrigir o problema de encaminhamento

Ao implementar uma Firewall do Azure numa sub-rede, uma etapa é criar uma rota predefinida para os pacotes de direcionar sub-rede através do endereço IP privado da firewall localizado no AzureFirewallSubnet. Para obter mais informações, consulte [Tutorial: Implementar e configurar a Firewall do Azure no portal do Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Quando introduz a firewall para o seu cenário de Balanceador de carga, pode entrar através do endereço IP público do seu firewall seu tráfego de Internet. A partir daí, que aplica suas regras de firewall e NATs os pacotes para o endereço IP público do seu Balanceador de carga. Isso é onde o problema ocorre. Pacotes chegam no endereço IP público do firewall, mas retornam para o firewall através do endereço IP privado (usando a rota predefinida).
Para evitar este problema, crie uma rota de anfitrião adicionais para o endereço IP público do firewall. Pacotes vai para o endereço IP público do firewall são encaminhados através da Internet. Isso evita a demorar a rota padrão para o endereço IP privado da firewall.

![Encaminhamento assimétrico](media/integrate-lb/Firewall-LB-asymmetric.png)

Por exemplo, as rotas seguintes destinam-se uma firewall no endereço IP público 13.86.122.41 e endereço IP privado 10.3.1.4.

![Tabela de rotas](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Balanceador de carga interno

Com um balanceador de carga interno, o Balanceador de carga é implementado com um endereço IP de front-end privado.

Não existe nenhum problema de encaminhamento assimétrico com este cenário. Os pacotes de entrada deparar-se com o endereço IP público do firewall, ser traduzidos para o endereço IP privado do Balanceador de carga e, em seguida, retorna para o endereço IP privado a firewall usando o mesmo caminho de retorno.

Então, pode implementar este cenário semelhante para o cenário de Balanceador de carga público, mas sem a necessidade do firewall rota de anfitrião do endereço IP público.

## <a name="additional-security"></a>Segurança adicional

Para melhorar ainda mais a segurança do seu cenário com balanceamento de carga, pode utilizar grupos de segurança de rede (NSGs).

Por exemplo, pode criar um NSG da sub-rede de back-end onde estão localizadas as máquinas de virtuais com balanceamento de carga. Permitir o tráfego de entrada provenientes da firewall IP endereço/porta.

Para obter mais informações sobre NSGs, consulte [grupos de segurança](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [implementar e configurar uma Firewall de Azure](tutorial-firewall-deploy-portal.md).