---
title: Ligue-se em privado a uma Aplicação Web usando o Ponto Final Privado do Azure
description: Ligue-se em privado a uma Aplicação Web usando o Ponto Final Privado do Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 893a7a2c7483fccc3bbc7bd198929f65917457b3
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036939"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Utilização de pontos finais privados para aplicação web azure (pré-visualização)

Pode utilizar o Private Endpoint para a sua Web App Azure para permitir que os clientes localizados na sua rede privada tenham acesso seguro à app sobre private link. O Private Endpoint utiliza um endereço IP do seu espaço de endereço Azure VNet. O tráfego de rede entre o cliente na sua rede privada e a Web App atravessa o Vnet e um Link Privado na rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública.

Utilizar o Private Endpoint para a sua Web App permite-lhe:

- Proteja a sua Aplicação Web configurando o Ponto Final do Serviço, eliminando a exposição pública
- Ligue-se de forma segura à Web App a partir de redes no local que se ligam ao Vnet utilizando um peering privado VPN ou ExpressRoute.

Se necessitar apenas de uma ligação segura entre o vnet e a sua Aplicação Web, o Ponto Final do Serviço é a solução mais simples. Se também precisa de chegar à aplicação web a partir do local através de um portal Azure, um Vnet regionalmente com um par ou um Vnet globalmente peered, Private Endpoint é a solução.  

Para mais informações sobre o [Ponto Final do Serviço][serviceendpoint]

## <a name="conceptual-overview"></a>Descrição geral conceptual

Um Endpoint privado é uma interface de rede especial (nic) para a sua Web App Azure na sua Subnet na sua Rede Virtual (Vnet).
Ao criar um Ponto Final Privado para a sua Web App, proporciona uma conectividade segura entre clientes na sua rede privada e na sua Web App. O Endpoint privado é atribuído um endereço IP da gama de endereços IP do seu Vnet.
A ligação entre o Ponto Final Privado e a Web App utiliza um [link privado][privatelink]seguro . O ponto final privado só é utilizado para os fluxos de entrada para a sua Web App. Os fluxos de saída não utilizarão este Ponto Final Privado, mas pode injetar fluxos de saída para a sua rede numa subrede diferente através da funcionalidade de [integração Vnet][vnetintegrationfeature].

A Subnet onde liga o Private Endpoint pode ter outros recursos, não precisa de uma Subnet vazia dedicada.
Pode implementar private endpoint numa região diferente da Web App. 

> [!Note]
>A funcionalidade de integração Vnet não pode usar a mesma sub-rede que o Private Endpoint, esta é uma limitação da funcionalidade de integração Vnet

Do ponto de vista da segurança:

- Quando ativa o Ponto Final do Serviço para a sua Web App, desativa todos os acessos públicos
- Você pode ativar vários pontos finais privados em outros Vnets e Subnets, incluindo Vnets em outras regiões
- O endereço IP do ponto final privado NIC deve ser dinâmico, mas permanecerá o mesmo até eliminar o Ponto Final Privado
- O NIC do Private Endpoint não pode ter um NSG associado
- A Subnet que acolhe o Private Endpoint pode ter um NSG associado, mas você deve desativar a aplicação das políticas de rede para o Private Endpoint ver [este artigo][disablesecuritype]. Como resultado, não pode filtrar por qualquer NSG o acesso ao seu Ponto Final Privado
- Quando ativa o Private Endpoint para a sua Web App, a configuração de [restrições][accessrestrictions] de acesso da Aplicação Web não é avaliada.
- Pode reduzir o risco de exfiltração de dados da vnet removendo todas as regras do NSG onde o destino é a marca de serviços internet ou Azure. Mas adicionar um Ponto Final do Serviço de Aplicações Web na sua subnet, permitirá que chegue a qualquer Web App hospedada no mesmo carimbo e exposta à Internet.

Private Endpoint for Web App está disponível para o nível PremiumV2, e isolado com uma ASE externa.

Nos registos da Web http da sua Web App, encontrará o IP de origem do cliente. Implementámos o protocolo TCP Proxy, encaminhando para a Web App a propriedade IP do cliente. Para obter mais informações, consulte [este artigo][tcpproxy].

![Visão geral global][1]


## <a name="dns"></a>DNS

Como esta funcionalidade está em pré-visualização, não alteramos a entrada de DNS durante a pré-visualização. Você precisa gerir a entrada DNS no seu servidor Privado DNS ou zona privada Azure DNS. Se precisar de usar um nome DNS personalizado, tem de adicionar o nome personalizado na sua Web App. Durante a pré-visualização, o nome personalizado deve ser validado como qualquer nome personalizado, utilizando a resolução pública de DNS. [referência técnica de validação dNS personalizada][dnsvalidation]

## <a name="pricing"></a>Preços

Para obter detalhes sobre preços, consulte o preço do [Link Privado Azure][pricing].

## <a name="limitations"></a>Limitações

Estamos a melhorar regularmente a funcionalidade Private Link e o Private Endpoint, consulte [este artigo][pllimitations] para obter informações atualizadas sobre limitações.

## <a name="next-steps"></a>Passos seguintes

Para implementar ponto final privado para a sua Web App através do portal, veja [como se conectar em privado a uma Web App][howtoguide]


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: https://docs.microsoft.com/azure/private-link/rivate-link-service-overview#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
