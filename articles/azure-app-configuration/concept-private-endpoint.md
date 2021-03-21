---
title: Utilização de pontos finais privados para configuração de aplicações Azure
description: Proteja a sua loja de configuração de aplicativos utilizando pontos finais privados
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 6cadadfb3623d05dd3ae3851acd5eaca13860023
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929848"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Utilização de pontos finais privados para configuração de aplicações Azure

Pode utilizar [pontos finais privados](../private-link/private-endpoint-overview.md) para configuração de aplicações Azure para permitir que os clientes de uma rede virtual (VNet) acedam de forma segura a dados por uma [ligação privada.](../private-link/private-link-overview.md) O ponto final privado utiliza um endereço IP a partir do espaço de endereço VNet para a sua loja de Configuração de Aplicações. O tráfego de rede entre os clientes do VNet e a loja de Configuração de Aplicações atravessa o VNet utilizando um link privado na rede de espinha dorsal da Microsoft, eliminando a exposição à internet pública.

A utilização de pontos finais privados para a sua loja de configuração de aplicações permite-lhe:
- Proteja os detalhes da configuração da sua aplicação configurando a firewall para bloquear todas as ligações à Configuração da Aplicação no ponto final público.
- Aumentar a segurança da rede virtual (VNet) garantindo que os dados não escapam do VNet.
- Ligue-se seguramente à loja de Configuração de Aplicações a partir de redes no local que se conectam ao VNet [utilizando VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../expressroute/expressroute-locations.md) com o seu olhar privado.

## <a name="conceptual-overview"></a>Descrição geral conceptual

Um ponto final privado é uma interface de rede especial para um serviço Azure na sua [Rede Virtual](../virtual-network/virtual-networks-overview.md) (VNet). Quando cria um ponto final privado para a sua loja App Config, proporciona uma conectividade segura entre os clientes no seu VNet e a sua loja de configuração. O ponto final privado é atribuído um endereço IP a partir do intervalo de endereço IP do seu VNet. A ligação entre o ponto final privado e a loja de configuração utiliza uma ligação privada segura.

As aplicações no VNet podem ligar-se à loja de configuração através do ponto final privado **utilizando as mesmas cordas de ligação e mecanismos de autorização que utilizariam de outra forma**. Os pontos finais privados podem ser utilizados com todos os protocolos suportados pela loja de Configuração de Aplicações.

Embora a Configuração da Aplicação não suporte pontos finais de serviço, os pontos finais privados podem ser criados em sub-redes que utilizam [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md). Os clientes numa sub-rede podem ligar-se de forma segura a uma loja de Configuração de Aplicações utilizando o ponto final privado enquanto utilizam pontos finais de serviço para aceder a outros.  

Quando cria um ponto final privado para um serviço no seu VNet, é enviado um pedido de consentimento para aprovação ao titular da conta de serviço. Se o utilizador que solicita a criação do ponto final privado for também proprietário da conta, este pedido de consentimento é automaticamente aprovado.

Os proprietários de conta de serviço podem gerir pedidos de consentimento e pontos finais privados através `Private Endpoints` do separador da loja config no [portal Azure.](https://portal.azure.com)

### <a name="private-endpoints-for-app-configuration"></a>Pontos finais privados para configuração de aplicativos 

Ao criar um ponto final privado, deve especificar a loja de Configuração de Aplicações a que se conecta. Se tiver várias instâncias de Configuração de Aplicações dentro de uma conta, precisa de um ponto final privado separado para cada loja.

### <a name="connecting-to-private-endpoints"></a>Ligação a pontos finais privados

O Azure baseia-se na resolução do DNS para encaminhar as ligações do VNet para a loja de configuração por um link privado. Pode rapidamente encontrar as cadeias de ligações no portal Azure selecionando a sua loja de Configuração de Aplicações e, em seguida, selecionando **As**  >  **Teclas de Acesso** de Definições .  

> [!IMPORTANT]
> Utilize o mesmo fio de ligação para ligar à sua loja de Configuração de Aplicações utilizando pontos finais privados como utilizaria para um ponto final público. Não se ligue à loja utilizando o seu `privatelink` URL de subdomínio.

## <a name="dns-changes-for-private-endpoints"></a>DNS alterações para pontos finais privados

Quando cria um ponto final privado, o registo de recursos DNS CNAME para a loja de configuração é atualizado para um pseudónimo num subdomínio com o prefixo `privatelink` . O Azure também cria uma [zona privada de DNS](../dns/private-dns-overview.md) correspondente ao `privatelink` subdomínio, com os registos de recursos DNS A para os pontos finais privados.

Quando resolve o URL de ponto final a partir do VNet que hospeda o ponto final privado, resolve-se para o ponto final privado da loja. Quando resolvido de fora do VNet, o URL do ponto final resolve-se para o ponto final público. Quando se cria um ponto final privado, o ponto final público é desativado.

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes devem ser capazes de resolver o nome de domínio totalmente qualificado (FQDN) para o ponto final do serviço para o endereço IP do ponto final privado. Configure o seu servidor DNS para delegar o seu subdomínio de ligação privada para a zona privada de DNS para o VNet, ou configure os registos A para `AppConfigInstanceA.privatelink.azconfig.io` com o endereço IP do ponto final privado.

> [!TIP]
> Ao utilizar um servidor DNS personalizado ou no local, deverá configurar o seu servidor DNS para resolver o nome da loja no `privatelink` subdomínio para o endereço IP do ponto final privado. Pode fazê-lo delegando o `privatelink` subdomínio para a zona privada de DNS do VNet, ou configurando a zona DNS no seu servidor DNS e adicionando os registos DNS A.

## <a name="pricing"></a>Preços

Permitir pontos finais privados requer uma loja de configuração de aplicativos [de nível padrão.](https://azure.microsoft.com/pricing/details/app-configuration/)  Para saber mais sobre os detalhes dos preços dos links privados, consulte os preços do [Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a criação de um ponto final privado para a sua loja de Configuração de Aplicações, consulte os seguintes artigos:

- [Criar um ponto final privado utilizando o Private Link Center no portal Azure](../private-link/create-private-endpoint-portal.md)
- [Criar um ponto final privado usando O Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Criar um ponto final privado usando a Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Aprenda a configurar o seu servidor DNS com pontos finais privados:

- [Name resolution for resources in Azure virtual networks](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) (Resolução de nomes para recursos em redes virtuais do Azure)
- [Configuração DNS para Pontos Finais Privados](../private-link/private-endpoint-overview.md#dns-configuration)