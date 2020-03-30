---
title: Utilização de pontos finais privados para configuração de aplicações do Azure
description: Proteja a sua loja de configuração de aplicações usando pontos finais privados
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366773"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Utilização de pontos finais privados para configuração de aplicações do Azure

Pode utilizar [pontos finais privados](../private-link/private-endpoint-overview.md) para a Configuração de Aplicações Azure para permitir que os clientes de uma rede virtual (VNet) acedam de forma segura a dados num [link privado](../private-link/private-link-overview.md). O ponto final privado utiliza um endereço IP do espaço de endereço VNet para a sua loja de configuração de aplicações. O tráfego de rede entre os clientes no VNet e a loja de configuração de aplicações atravessa o VNet usando um link privado na rede de espinha dorsal da Microsoft, eliminando a exposição à internet pública.

Utilizar pontos finais privados para a sua loja de configuração de aplicações permite-lhe:
- Proteja os detalhes da configuração da sua aplicação configurando a firewall para bloquear todas as ligações à Configuração da Aplicação no ponto final público.
- Aumente a segurança da rede virtual (VNet) garantindo que os dados não escapam do VNet.
- Ligue-se de forma segura à loja de configuração de aplicações a partir de redes no local que se ligam à VNet utilizando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../expressroute/expressroute-locations.md) com private-peering.

> [!NOTE]
> A Configuração de Aplicações Azure oferece o uso de pontos finais privados como pré-visualização pública. As ofertas de pré-visualização pública permitem aos clientes experimentar novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização pública não se destinam à utilização da produção.

## <a name="conceptual-overview"></a>Descrição geral conceptual

Um ponto final privado é uma interface de rede especial para um serviço Azure na sua [Rede Virtual](../virtual-network/virtual-networks-overview.md) (VNet). Ao criar um ponto final privado para a sua loja App Config, proporciona uma conectividade segura entre os clientes na sua VNet e na sua loja de configuração. O ponto final privado é atribuído um endereço IP da gama de endereços IP do seu VNet. A ligação entre o ponto final privado e a loja de configuração utiliza um link privado seguro.

As aplicações no VNet podem ligar-se à loja de configuração sobre o ponto final privado utilizando as mesmas cordas de **ligação e mecanismos**de autorização que utilizariam de outra forma . Os pontos finais privados podem ser utilizados com todos os protocolos suportados pela loja de configuração de aplicações.

Embora a Configuração da Aplicação não suporte pontos finais do serviço, os pontos finais privados podem ser criados em subredes que utilizam [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)de serviço . Os clientes numa subnet podem ligar-se de forma segura a uma loja de Configuração de Aplicações utilizando o ponto final privado, utilizando pontos finais de serviço para aceder a outros.  

Quando cria um ponto final privado para um serviço no seu VNet, é enviado um pedido de consentimento para aprovação ao proprietário da conta de serviço. Se o utilizador que solicita a criação do ponto final privado for também proprietário da conta, este pedido de consentimento é automaticamente aprovado.

Os proprietários de conta de serviço podem `Private Endpoints` gerir pedidos de consentimento e pontos finais privados através do separador da loja de config no [portal Azure](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Pontos finais privados para configuração de apps 

Ao criar um ponto final privado, deve especificar a loja de configuração da aplicação à qual se conecta. Se tiver várias instâncias de Configuração de Aplicações dentro de uma conta, precisa de um ponto final privado separado para cada loja.

### <a name="connecting-to-private-endpoints"></a>Ligação a pontos finais privados

O Azure conta com a resolução do DNS para encaminhar as ligações da VNet para a loja de configuração sobre um link privado. Pode encontrar rapidamente cordas de ligações no portal Azure selecionando a sua loja de configuração de aplicações e, em seguida, selecionando**As Teclas**de Acesso de **Definições** > .  

> [!IMPORTANT]
> Utilize a mesma cadeia de ligação para se ligar à sua loja de configuração de aplicações utilizando pontos finais privados como utilizaria para um ponto final público. Não se conecto à `privatelink` conta de armazenamento utilizando o URL do subdomínio.

## <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos finais privados

Quando cria um ponto final privado, o registo de recursos DNS CNAME para a loja `privatelink`de configuração é atualizado para um pseudónimo num subdomínio com o prefixo . O Azure também cria uma zona `privatelink` privada de [DNS](../dns/private-dns-overview.md) correspondente ao subdomínio, com os registos de recursos DNS A para os pontos finais privados.

Quando resolve o URL de ponto final de fora da VNet, resolve-se até ao ponto final da loja. Quando resolvido a partir do VNet que acolhe o ponto final privado, o URL de ponto final resolve-se até ao ponto final privado.

Pode controlar o acesso a clientes fora do VNet através do ponto final público utilizando o serviço Azure Firewall.

Esta abordagem permite o acesso à loja utilizando a mesma cadeia de **ligação** para clientes no VNet que acolhe os pontos finais privados, bem como clientes fora do VNet.

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes devem ser capazes de resolver o nome de domínio totalmente qualificado (FQDN) para o ponto final do serviço para o endereço IP do ponto final privado. Configure o seu servidor DNS para delegar o seu subdomínio de ligação privada `AppConfigInstanceA.privatelink.azconfig.io` na zona privada de DNS para o VNet, ou configurar os registos A com o endereço IP final do ponto final privado.

> [!TIP]
> Ao utilizar um servidor DNS personalizado ou no local, deve configurar o seu `privatelink` servidor DNS para resolver o nome da loja no subdomínio para o endereço IP do ponto final privado. Pode fazê-lo delegando o `privatelink` subdomínio na zona privada de DNS do VNet, ou configurando a zona DNS no seu servidor DNS e adicionando os registos DNS A.

## <a name="pricing"></a>Preços

Ativar pontos finais privados requer uma loja de configuração de aplicações [de nível Standard.](https://azure.microsoft.com/pricing/details/app-configuration/)  Para saber mais sobre os detalhes de preços de ligação privada, consulte o preço do [Link Privado Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a criação de um ponto final privado para a sua loja de Configuração de Aplicações, consulte os seguintes artigos:

- [Criar um ponto final privado usando o Private Link Center no portal Azure](../private-link/create-private-endpoint-portal.md)
- [Criar um ponto final privado usando o Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Criar um ponto final privado usando o Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Aprenda a configurar o seu servidor DNS com pontos finais privados:

- [Name resolution for resources in Azure virtual networks](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server) (Resolução de nomes para recursos em redes virtuais do Azure)
- [Configuração DNS para pontos finais privados](/azure/private-link/private-endpoint-overview#dns-configuration)
