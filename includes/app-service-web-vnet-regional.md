---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 06/08/2020
ms.author: ccompy
ms.openlocfilehash: 9259e3d3e41a5ba4986fbef48e745210f80e6093
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91255261"
---
A utilização da Integração VNet regional permite que a sua aplicação aceda:

* Recursos num VNet na mesma região que a sua aplicação.
* Os recursos em VNets espreguiçadados para o VNet a sua aplicação está integrada.
* Serviços seguros de serviço.
* Recursos através das ligações Azure ExpressRoute.
* Recursos no VNet com os qual está integrado.
* Recursos através de ligações espreitadas, que incluem ligações Azure ExpressRoute.
* Pontos finais privados 

Quando utilizar a Integração VNet com VNets na mesma região, pode utilizar as seguintes funcionalidades de networking Azure:

* **Grupos de segurança de rede (NSGs)**: Pode bloquear o tráfego de saída com um NSG colocado na sua sub-rede de integração. As regras de entrada não se aplicam porque não pode usar a Integração VNet para fornecer acesso à sua aplicação.
* **Tabelas de rotas (UDRs)**: Pode colocar uma tabela de rota na sub-rede de integração para enviar o tráfego de saída onde quiser.

Por padrão, a sua aplicação apenas encaminha o tráfego RFC1918 para o seu VNet. Se quiser encaminhar todo o tráfego de saída para o seu VNet, aplique a definição da aplicação WEBSITE_VNET_ROUTE_ALL para a sua aplicação. Para configurar a definição da aplicação:

1. Aceda ao **UI de configuração** no portal da aplicação. Selecione **Nova definição da aplicação**.
1. Introduza **WEBSITE_VNET_ROUTE_ALL** na caixa **Nome** e introduza **1** na caixa **Valor.**

   ![Fornecer definição de aplicação][4]

1. Selecione **OK**.
1. Selecione **Guardar**.

Se encaminhar todo o tráfego de saída para o seu VNet, está sujeito aos NSGs e UDRs que são aplicados na sua sub-rede de integração. Quando encaminha todo o tráfego de saída para o seu VNet, os seus endereços de saída ainda são os endereços de saída que estão listados nas propriedades da sua aplicação, a menos que forneça rotas para enviar o tráfego para outro lugar.

Existem algumas limitações com a utilização da Integração VNet com VNets na mesma região:

* Não se pode alcançar recursos através de ligações globais de observação.
* A funcionalidade está disponível apenas a partir de novas unidades de escala do Azure App Service que suportam planos do Serviço de Aplicações PremiumV2. Note que *isso não significa que a sua aplicação deve funcionar num nível de preços PremiumV2*, apenas que deve funcionar num Plano de Serviço de Aplicações onde a opção PremiumV2 está disponível (o que implica que é uma unidade de escala mais recente onde esta funcionalidade de integração VNet também está disponível).
* A sub-rede de integração pode ser utilizada apenas por um plano de Serviço de Aplicações.
* A funcionalidade não pode ser utilizada por aplicações de plano isolado que se encontrem num Ambiente de Serviço de Aplicações.
* A funcionalidade requer uma sub-rede não utilizada que seja uma /27 com 32 endereços ou maior num VNet do Gestor de Recursos Azure.
* A aplicação e o VNet devem estar na mesma região.
* Não é possível eliminar um VNet com uma aplicação integrada. Remova a integração antes de eliminar o VNet.
* Só é possível integrar-se com VNets na mesma subscrição que a aplicação.
* Você pode ter apenas uma integração regional VNet por plano de Serviço de Aplicação. Várias aplicações no mesmo plano de Serviço de Aplicações podem usar o mesmo VNet.
* Não é possível alterar a subscrição de uma app ou de um plano enquanto há uma aplicação que está a usar a Integração VNet regional.
* A sua aplicação não consegue resolver endereços em Zonas Privadas Azure DNS sem alterações de configuração

Um endereço é usado para cada instância de plano. Se escalar a sua aplicação em cinco instâncias, então são utilizados cinco endereços. Uma vez que o tamanho da sub-rede não pode ser alterado após a atribuição, você deve usar uma sub-rede que é grande o suficiente para acomodar qualquer escala que a sua app possa alcançar. Um /26 com 64 endereços é o tamanho recomendado. A /26 com 64 endereços acomoda um plano Premium com 30 instâncias. Quando escala um plano para cima ou para baixo, você precisa do dobro de endereços por um curto período de tempo.

Se quiser que as suas apps num outro plano cheguem a um VNet que já esteja ligado a apps noutro plano, selecione uma sub-rede diferente da que está a ser usada pela integração VNet pré-existente.

A funcionalidade é totalmente suportada tanto para aplicações Windows como Linux, incluindo [recipientes personalizados.](../articles/app-service/quickstart-custom-container.md) Todos os comportamentos agem da mesma forma entre aplicações Windows e aplicações Linux.

### <a name="service-endpoints"></a>Pontos finais de serviço

A Integração Regional de VNet permite-lhe utilizar pontos finais de serviço. Para utilizar pontos finais de serviço com a sua aplicação, utilize a Integração Regional de VNet para ligar a um VNet selecionado e, em seguida, configure os pontos finais do serviço com o serviço de destino na sub-rede que utilizou para a integração. Se então quisesse aceder a um serviço sobre os pontos finais de serviço:

1. Configure a Integração Regional de VNet com a sua aplicação web
1. ir para o serviço de destino e configurar pontos finais de serviço contra a sub-rede utilizada para a integração

### <a name="network-security-groups"></a>Grupos de segurança de rede

Pode utilizar grupos de segurança de rede para bloquear o tráfego de entrada e saída de recursos num VNet. Uma aplicação que utiliza a Integração VNet regional pode usar um [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos no seu VNet ou na internet. Para bloquear o tráfego para endereços públicos, tem de ter a definição de aplicação WEBSITE_VNET_ROUTE_ALL definida para 1. As regras de entrada num NSG não se aplicam à sua aplicação porque a Integração VNet afeta apenas o tráfego de saída da sua aplicação.

Para controlar o tráfego de entrada na sua aplicação, utilize a funcionalidade Restrições de Acesso. Um NSG aplicado à sua sub-rede de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL estiver definido para 1 e não tiver rotas que afetem o tráfego de endereço público na sua sub-rede de integração, todo o tráfego de saída ainda está sujeito a NSGs atribuídos à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL não estiver definido, os NSGs são aplicados apenas ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Pode utilizar as tabelas de rotas para encaminhar o tráfego de saída da sua app para onde quiser. Por predefinição, as tabelas de rotas apenas afetam o tráfego de destino RFC1918. Se definir WEBSITE_VNET_ROUTE_ALL para 1, todas as suas chamadas de saída são afetadas. As rotas que estão definidas na sua sub-rede de integração não afetarão respostas a pedidos de aplicações de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways.

Se quiser encaminhar todo o tráfego de saída no local, pode utilizar uma mesa de rota para enviar todo o tráfego de saída para o seu gateway ExpressRoute. Se fizer a rota do tráfego para um gateway, certifique-se de definir rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetam o tráfego da sua aplicação. Se tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída da sua aplicação será afetado. Por padrão, as rotas BGP afetam apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL estiver definido para 1, todo o tráfego de saída pode ser afetado pelas suas rotas BGP.

### <a name="azure-dns-private-zones"></a>Zonas Privadas Azure DNS 

Depois de a sua aplicação se integrar com o seu VNet, utiliza o mesmo servidor DNS com o qual o seu VNet está configurado. Por padrão, a sua aplicação não funcionará com as Zonas Privadas Azure DNS. Para trabalhar com as Zonas Privadas Azure DNS, é necessário adicionar as seguintes definições de aplicações:

1. WEBSITE_DNS_SERVER com o valor 168.63.129.16 
1. WEBSITE_VNET_ROUTE_ALL com o valor 1

Estas definições enviarão todas as suas chamadas de saída da sua app para o seu VNet, além de permitir que a sua aplicação utilize zonas privadas Azure DNS.

### <a name="private-endpoints"></a>Pontos finais privados

Se quiser fazer chamadas para [Private Endpoints,][privateendpoints]então tem de se integrar com as Zonas Privadas Azure DNS ou gerir o ponto final privado no servidor DNS utilizado pela sua aplicação. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
