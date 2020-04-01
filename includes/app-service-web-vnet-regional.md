---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: e0db3ce7d31b838ca6f7d566083a33ee215d3399
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419550"
---
A utilização da Integração VNet regional permite ao acesso da sua aplicação:

* Recursos na rede virtual na mesma região com que se integra.
* Recursos em redes virtuais espreitaram para a sua rede virtual que estão na mesma região.
* Serviço endpoint garantido serviços.
* Recursos através das ligações Azure ExpressRoute.
* Recursos na rede virtual a que está ligado.
* Recursos através de conexões com pares, que inclui ligações Azure ExpressRoute.
* Pontos finais privados.

Quando utiliza a Integração VNet com redes virtuais na mesma região, pode utilizar as seguintes funcionalidades de networking Azure:

* Grupos de segurança de **rede (NSGs)**: Pode bloquear o tráfego de saída com um NSG colocado na sua subnet de integração. As regras de entrada não se aplicam porque não pode utilizar a Integração VNet para fornecer acesso de entrada à sua aplicação.
* **Tabelas de rotas (UDRs)**: Pode colocar uma tabela de rotas na subnet de integração para enviar tráfego de saída onde quiser.

Por padrão, a sua aplicação apenas direciona o tráfego RFC1918 para a sua rede virtual. Se quiser encaminhar todo o tráfego de saída para a sua rede virtual, aplique a definição de aplicações WEBSITE_VNET_ROUTE_ALL na sua aplicação. Para configurar a definição da aplicação:

1. Vá ao UI de **Configuração** no portal da sua aplicação. Selecione **nova definição de aplicação**.
1. Introduza **WEBSITE_VNET_ROUTE_ALL** na caixa **Nome** e introduza **1** na caixa **Valor.**

   ![Fornecer a definição de aplicações][4]

1. Selecione **OK**.
1. Selecione **Guardar**.

Se encaminhar todo o tráfego de saída para a sua rede virtual, está sujeito aos NSGs e UDRs que são aplicados à sua subnet de integração. Ao encaminhar todo o tráfego de saída para a sua rede virtual, os seus endereços de saída ainda são os endereços de saída que estão listados nas propriedades da sua aplicação, a menos que forneça rotas para enviar o tráfego para outro lugar.

Existem algumas limitações na utilização da Integração VNet com redes virtuais na mesma região:

* Não se pode alcançar recursos através de ligações globais de observação.
* A funcionalidade está disponível apenas a partir de unidades de escala mais recentes do Serviço de Aplicações Azure que suportam planos premiumV2 App Service.
* A sub-rede de integração só pode ser utilizada por um plano de Serviço de Aplicações.
* A funcionalidade não pode ser utilizada por aplicações de plano isoladoque se encontram num Ambiente de Serviço de Aplicações.
* A funcionalidade requer uma sub-rede não utilizada que seja um /27 com 32 endereços ou maior numa rede virtual do Gestor de Recursos Azure.
* A aplicação e a rede virtual devem estar na mesma região.
* Não é possível eliminar uma rede virtual com uma aplicação integrada. Remova a integração antes de eliminar a rede virtual.
* Só pode integrar redes virtuais na mesma subscrição que a app.
* Você pode ter apenas uma Integração VNet regional por plano de serviço de aplicações. Várias aplicações no mesmo plano de Serviço de Aplicações podem usar a mesma rede virtual.
* Não é possível alterar a subscrição de uma app ou de um plano enquanto há uma aplicação que está a usar a Integração VNet regional.

Um endereço é usado para cada instância de plano. Se escalar a sua aplicação para cinco instâncias, então são utilizados cinco endereços. Uma vez que o tamanho da subrede não pode ser alterado após a atribuição, você deve usar uma subnet que é grande o suficiente para acomodar qualquer escala que a sua app possa alcançar. A /26 com 64 endereços é o tamanho recomendado. A /26 com 64 endereços acomoda um plano Premium com 30 instâncias. Quando escala um plano para cima ou para baixo, precisa do dobro dos endereços por um curto período de tempo.

Se quiser que as suas apps noutro plano cheguem a uma rede virtual que já esteja ligada por aplicações noutro plano, selecione uma subnet diferente da utilizada pela integração vnet pré-existente.

A funcionalidade está em pré-visualização do Linux. A forma Linux da funcionalidade apenas suporta a realização de chamadas para endereços RFC 1918 (10.0.0.0.0/8, 172.16.0.0/12, 192.168.0.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Web ou App de Funções para Contentores

Se hospedar a sua aplicação no Linux com as imagens incorporadas, a VNet Integration regional funciona sem alterações adicionais. Se utilizar a Web ou a App de Função para contentores, tem de modificar a sua imagem de estivador para utilizar a Integração VNet. Na sua imagem de estivador, utilize a variável do ambiente PORT como porta de escuta do servidor web principal, em vez de utilizar um número de porta codificado. A variável ambiente PORT é automaticamente definida pela plataforma no tempo de arranque do contentor. Se utilizar o SSH, o daemon SSH deve ser configurado para ouvir o número de porta especificado pela variável ambiente SSH_PORT quando utilizar a Integração Regional vNet. Não há apoio para a integração vnet exigida por gateway em Linux.

### <a name="service-endpoints"></a>Pontos finais de serviço

Com a Integração VNet regional, pode utilizar pontos finais de serviço. Para utilizar pontos finais de serviço com a sua aplicação, utilize a Integração VNet regional para se ligar a uma rede virtual selecionada. Em seguida, configure os pontos finais do serviço na subnet que utilizou para a integração.

### <a name="network-security-groups"></a>Grupos de segurança de rede

Você pode usar grupos de segurança de rede para bloquear tráfego de entrada e saída para recursos em uma rede virtual. Uma aplicação que utiliza a VNet Integration regional pode usar um grupo de segurança de [rede][VNETnsg] para bloquear o tráfego de saída para recursos na sua rede virtual ou na internet. Para bloquear o tráfego para endereços públicos, deve ter a definição de aplicação WEBSITE_VNET_ROUTE_ALL definida para 1. As regras de entrada num NSG não se aplicam à sua aplicação porque a VNet Integration afeta apenas o tráfego de saída da sua aplicação.

Para controlar o tráfego de entrada na sua aplicação, utilize a funcionalidade Restrições de Acesso. Um NSG aplicado à sua subnet de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL está definido para 1 e não tiver nenhuma rota que afete o tráfego de endereços públicos na sua subnet de integração, todo o seu tráfego de saída ainda está sujeito a NSGs atribuídos à sua subnet de integração. Se WEBSITE_VNET_ROUTE_ALL não estiver definido, os NSGs são aplicados apenas ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Pode utilizar tabelas de rotas para direcionar o tráfego de saída da sua app para onde quiser. Por padrão, as tabelas de rotas apenas afetam o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL para 1, todas as suas chamadas de saída são afetadas. As rotas definidas na sua subnet de integração não afetarão as respostas aos pedidos de aplicações de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways.

Se quiser encaminhar todo o tráfego de saída no local, pode utilizar uma mesa de rota para enviar todo o tráfego de saída para o seu gateway ExpressRoute. Se fizer o tráfego de encaminhamento para um portal, certifique-se de que define rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetam o tráfego da sua aplicação. Se tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída da sua aplicação será afetado. Por predefinição, as rotas BGP afetam apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL estiver definido para 1, todo o tráfego de saída pode ser afetado pelas suas rotas de BGP.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/