---
title: Ligue-se em privado a uma Aplicação Web usando o Ponto Final Privado do Azure
description: Ligue-se em privado a uma Aplicação Web usando o Ponto Final Privado do Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4d139cfa50afa94621066995314737fac70bbafe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756274"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Utilização de pontos finais privados para aplicação web azure (pré-visualização)

> [!Note]
> A pré-visualização está disponível nas regiões leste dos EUA e US 2 para todas as aplicações PremiumV2 Windows e Linux Web e Funções Elásticas Premium. 

Pode utilizar o Private Endpoint para a sua Web App Azure para permitir que os clientes localizados na sua rede privada acedam de forma segura à aplicação sobre private link. O Private Endpoint utiliza um endereço IP do seu espaço de endereço Azure VNet. O tráfego de rede entre um cliente na sua rede privada e a Web App atravessa o VNet e um Link Privado na rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública.

Utilizar o Private Endpoint para a sua Web App permite-lhe:

- Proteja a sua Aplicação Web configurando o Ponto Final Privado, eliminando a exposição pública.
- Ligue-se de forma segura à Web App a partir de redes no local que se ligam à VNet utilizando um peering privado VPN ou ExpressRoute.

Se precisa apenas de uma ligação segura entre o vNet e a sua Aplicação Web, um Ponto final de serviço é a solução mais simples. Se também precisa de chegar à aplicação web a partir do local através de um portal Azure, um VNet regionalmente com um par ou um VNet globalmente peered, Private Endpoint é a solução.  

Para mais informações, consulte [Pontos Finais de Serviço][serviceendpoint].

## <a name="conceptual-overview"></a>Descrição geral conceptual

Um Private Endpoint é uma interface de rede especial (NIC) para a sua Web App Azure numa Subnet na sua Rede Virtual (VNet).
Ao criar um Ponto Final Privado para a sua Web App, proporciona conectividade segura entre clientes na sua rede privada e na sua Web App. O Ponto Final Privado é atribuído um endereço IP da gama de endereços IP do seu VNet.
A ligação entre o Ponto Final Privado e a Web App utiliza um [link privado][privatelink]seguro . O Private Endpoint só é utilizado para os fluxos de entrada para a sua Web App. Os fluxos de saída não utilizarão este Ponto Final Privado, mas pode injetar fluxos de saída para a sua rede numa subrede diferente através da funcionalidade de [integração VNet][vnetintegrationfeature].

A Subnet onde liga o Private Endpoint pode ter outros recursos, não precisa de uma Subnet vazia dedicada.
Também pode implementar o Private Endpoint numa região diferente da Web App. 

> [!Note]
>A funcionalidade de integração VNet não pode usar a mesma sub-rede que o Private Endpoint, esta é uma limitação da funcionalidade de integração VNet.

Do ponto de vista da segurança:

- Quando ativa os Pontos Finais Privados para a sua Web App, desativa todos os acessos públicos.
- Você pode ativar vários pontos finais privados em outros VNets e Subnets, incluindo VNets em outras regiões.
- O endereço IP do Nic private Endpoint deve ser dinâmico, mas permanecerá o mesmo até eliminar o Ponto Final Privado.
- O NIC do Private Endpoint não pode ter um NSG associado.
- A Subnet que acolhe o Private Endpoint pode ter um NSG associado, mas deve desativar a aplicação das políticas de rede para o Ponto Final Privado: ver Políticas de [rede de desativação para pontos finais privados][disablesecuritype]. Como resultado, não é possível filtrar por nenhum NSG o acesso ao seu Ponto Final Privado.
- Quando ativa o Private Endpoint para a sua Web App, a configuração de [restrições][accessrestrictions] de acesso da Aplicação Web não é avaliada.
- Pode reduzir o risco de exfiltração de dados do VNet removendo todas as regras do NSG onde o destino é a marca de serviços internet ou Azure. Mas adicionar um Web App Private Endpoint na sua subnet permitirá que você chegue a qualquer Web App hospedada no mesmo carimbo de implementação e exposta à Internet.

Nos registos web HTTP da sua Web App, encontrará o IP de origem do cliente. Isto é implementado utilizando o protocolo TCP Proxy, reencaminhando a propriedade IP do cliente até à Web App. Para mais informações, consulte Obter informações sobre [ligação utilizando o TCP Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Visão global do ponto de vista global do Web App Private Endpoint](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Como esta funcionalidade está em pré-visualização, não alteramos a entrada de DNS durante a pré-visualização. Você precisa gerir a entrada de DNS no seu servidor Privado DNS ou na zona privada Azure DNS.
Se precisar de usar um nome DNS personalizado, tem de adicionar o nome personalizado na sua Web App. Durante a pré-visualização, o nome personalizado deve ser validado como qualquer nome personalizado, utilizando a resolução pública de DNS. Consulte [a validação personalizada do DNS][dnsvalidation] para obter mais informações.

## <a name="pricing"></a>Preços

Para obter detalhes sobre preços, consulte o preço do [Link Privado Azure][pricing].

## <a name="limitations"></a>Limitações

Estamos a melhorar regularmente a funcionalidade Private Link e o Private Endpoint, consulte [este artigo][pllimitations] para obter informações atualizadas sobre limitações.

## <a name="next-steps"></a>Passos seguintes

Para implementar ponto final privado para a sua Web App através do portal, veja [como se conectar em privado a uma Web App][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
