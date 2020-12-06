---
title: Integração virtual da rede de serviços Azure para isolamento de rede
titlesuffix: Azure Virtual Network
description: Este artigo descreve diferentes métodos de integração de um serviço Azure a uma rede virtual que lhe permite aceder de forma segura ao serviço Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 4a6cd529511d4a2e71e1a31c1600f8a51f455a37
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746136"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Integrar serviços Azure com redes virtuais para isolamento de rede

A integração da Rede Virtual (VNet) para um serviço Azure permite-lhe bloquear o acesso ao serviço apenas para a sua infraestrutura de rede virtual. A infraestrutura VNet também inclui redes virtuais e redes no local.

A integração VNet proporciona aos serviços Azure os benefícios do isolamento da rede e pode ser realizada por um ou mais dos seguintes métodos:
- [Implantar instâncias dedicadas do serviço numa rede virtual.](virtual-network-service-endpoints-overview.md) Os serviços podem então ser acedidos de forma privada dentro da rede virtual e a partir de redes no local.
- Utilizar [o Private Endpoint](../private-link/private-endpoint-overview.md) que o liga de forma privada e segura a um serviço alimentado pela [Azure Private Link](../private-link/private-link-overview.md). O Private Endpoint utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para a sua rede virtual.
- Aceder ao serviço utilizando pontos finais públicos, alargando uma rede virtual ao serviço, através [de pontos finais de serviço.](virtual-network-service-endpoints-overview.md) Os pontos finais de serviço permitem que os recursos de serviço sejam seguros para a rede virtual.
- Utilizar [tags de serviço](service-tags-overview.md) para permitir ou negar tráfego aos seus recursos Azure de e para os pontos finais IP públicos.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Implementar serviços dedicados da Azure em redes virtuais

Quando implementa serviços Azure dedicados numa rede virtual, pode comunicar com os recursos de serviço em privado, através de endereços IP privados.

![Implementar serviços dedicados da Azure em redes virtuais](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A implementação de um serviço Azure dedicado na sua rede virtual fornece as seguintes capacidades:
- Os recursos dentro da rede virtual podem comunicar-se entre si em privado, através de endereços IP privados. Exemplo, transferindo diretamente dados entre o HDInsight e o SQL Server a funcionar numa máquina virtual, na rede virtual.
- Os recursos no local podem aceder a recursos numa rede virtual utilizando endereços IP privados através de uma VPN site-to-site (VPN Gateway) ou ExpressRoute.
- As redes virtuais podem ser espreitadas para permitir que os recursos nas redes virtuais se comuniquem entre si, utilizando endereços IP privados.
- As instâncias de serviço numa rede virtual são normalmente totalmente geridas pelo serviço Azure. Isto inclui monitorizar a saúde dos recursos e escalar com carga.
- As instâncias de serviço são implantadas numa sub-rede numa rede virtual. O acesso à rede de entrada e saída da sub-rede deve ser aberto através de grupos de segurança de rede, por orientação fornecida pelo serviço.
- Certos serviços impõem também restrições à sub-rede em que são implantados, limitando a aplicação de políticas, rotas ou combinando VMs e recursos de serviço dentro da mesma sub-rede. Consulte cada serviço sobre as restrições específicas, uma vez que podem mudar ao longo do tempo. Exemplos destes serviços são ficheiros Azure NetApp, HSM dedicado, Instâncias de Contentores Azure, Serviço de Aplicações.
- Opcionalmente, os serviços podem exigir uma sub-rede delegada como um identificador explícito de que uma sub-rede pode hospedar um determinado serviço. Ao delegar, os serviços obtêm permissões explícitas para criar recursos específicos do serviço na sub-rede delegada.
- Veja um exemplo de uma resposta API REST numa rede virtual com uma sub-rede delegada. Uma lista completa de serviços que utilizam o modelo de sub-redes delegado pode ser obtida através da API das Delegações Disponíveis.

Para obter uma lista de serviços que podem ser implantados numa rede virtual, consulte [os serviços de Azure dedicados em redes virtuais.](virtual-network-for-azure-services.md)

## <a name="private-link-and-private-endpoints"></a>Link Privado e Pontos Finais Privados

Você pode usar pontos finais privados para permitir ingresssões de eventos diretamente da sua rede virtual para o recurso Azure de forma segura através de um link privado sem passar pela internet pública. Um ponto final privado é uma interface de rede especial para um serviço Azure na sua rede virtual. Quando cria um ponto final privado para o seu recurso Azure, proporciona uma conectividade segura entre os clientes na sua rede virtual e o seu recurso Azure. O ponto final privado é atribuído um endereço IP a partir do intervalo de endereços IP da sua rede virtual. A ligação entre o ponto final privado e o serviço Azure utiliza uma ligação privada segura.

O exemplo a seguir mostra o acesso privado de um ponto final privado de recursos de Event Grid que proporciona conectividade segura entre clientes numa rede virtual e recurso de Grade de Eventos.

![Acesso privado de recurso DB SQL utilizando Private Endpoint](./media/network-isolation/architecture-diagram.png)

Para obter mais informações sobre o Private Link e uma lista de serviços Azure que são suportados, veja [O que é Private Link?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Pontos finais de serviço
O ponto final do serviço VNet proporciona conectividade segura e direta aos serviços Azure durante uma rota otimizada sobre a rede de espinha dorsal Azure. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O Service Endpoints permite que os endereços IP privados no VNet cheguem ao ponto final de um serviço Azure sem precisar de um endereço IP público no VNet.

![Proteger serviços do Azure para redes virtuais](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Para mais informações, consulte [pontos finais do serviço de rede Virtual](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>Etiquetas de serviço

Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. Utilizando etiquetas de serviço, pode definir controlos de acesso à rede em [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) de rede ou [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags). Ao especificar o nome da etiqueta de serviço (por exemplo, AzureEventGrid) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.

![Permitir ou negar tráfego usando Tags de Serviço](./media/network-isolation/service-tags.png)

Pode utilizar tags de serviço para alcançar o isolamento da rede e proteger os seus recursos Azure da Internet geral, ao mesmo tempo que acede aos serviços Azure que têm pontos finais públicos. Crie regras de grupo de segurança de rede de entrada/saída para negar o tráfego de/para a **Internet** e permitir o tráfego de/para **a AzureCloud** ou outras tags de serviço disponíveis de [serviços específicos](service-tags-overview.md#available-service-tags) da Azure.

Para obter mais informações sobre tags de serviço e serviços Azure que os suportam, consulte [Visão geral das Etiquetas de Serviço](service-tags-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba como integrar a [sua aplicação com uma rede Azure.](../app-service/web-sites-integrate-with-vnet.md)
- Saiba como restringir o [acesso a recursos utilizando tags de serviço.](tutorial-restrict-network-access-to-resources.md)
- Saiba como [ligar-se privadamente a uma conta Azure Cosmos utilizando o Azure Private Link](../private-link/create-private-endpoint-cosmosdb-portal.md).