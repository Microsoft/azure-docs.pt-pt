---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604886"
---
A utilização da Integração VNet regional permite ao acesso da sua aplicação:

* Recursos num VNet na mesma região que a sua aplicação.
* Recursos em VNets espreitados para o VNet a sua aplicação está integrada.
* Serviço endpoint garantido serviços.
* Recursos através das ligações Azure ExpressRoute.
* Recursos na VNet com os que está integrado.
* Recursos através de conexões com pares, que inclui ligações Azure ExpressRoute.
* Pontos finais privados 

Quando utilizar a Integração VNet com VNets na mesma região, pode utilizar as seguintes funcionalidades de networking Azure:

* Grupos de segurança de **rede (NSGs)**: Pode bloquear o tráfego de saída com um NSG colocado na sua subnet de integração. As regras de entrada não se aplicam porque não pode utilizar a Integração VNet para fornecer acesso de entrada à sua aplicação.
* **Tabelas de rotas (UDRs)**: Pode colocar uma tabela de rotas na subnet de integração para enviar tráfego de saída onde quiser.

Por padrão, a sua aplicação apenas direciona o tráfego RFC1918 para o seu VNet. Se quiser encaminhar todo o tráfego de saída para o seu VNet, aplique a definição de aplicação WEBSITE_VNET_ROUTE_ALL na sua aplicação. Para configurar a definição da aplicação:

1. Vá ao UI de **Configuração** no portal da sua aplicação. Selecione **nova definição de aplicação**.
1. Introduza **WEBSITE_VNET_ROUTE_ALL** na caixa **Nome** e introduza **1** na caixa **Valor.**

   ![Fornecer a definição de aplicações][4]

1. Selecione **OK**.
1. Selecione **Guardar**.

Se encaminhar todo o tráfego de saída para o seu VNet, está sujeito aos NSGs e UDRs que são aplicados à sua subnet de integração. Ao encaminhar todo o tráfego de saída para o seu VNet, os seus endereços de saída ainda são os endereços de saída que estão listados nas propriedades da sua aplicação, a menos que forneça rotas para enviar o tráfego para outro lugar.

Existem algumas limitações na utilização da Integração VNet com VNets na mesma região:

* Não se pode alcançar recursos através de ligações globais de observação.
* A funcionalidade está disponível apenas a partir de unidades de escala mais recentes do Serviço de Aplicações Azure que suportam planos premiumV2 App Service.
* A sub-rede de integração só pode ser utilizada por um plano de Serviço de Aplicações.
* A funcionalidade não pode ser utilizada por aplicações de plano isoladoque se encontram num Ambiente de Serviço de Aplicações.
* A funcionalidade requer uma sub-rede não utilizada que seja um /27 com 32 endereços ou maior num VNet do Gestor de Recursos Azure.
* A aplicação e o VNet devem estar na mesma região.
* Não é possível apagar um VNet com uma aplicação integrada. Retire a integração antes de eliminar o VNet.
* Só pode integrar-se com VNets na mesma subscrição que a app.
* Você pode ter apenas uma Integração VNet regional por plano de serviço de aplicações. Várias aplicações no mesmo plano de Serviço de Aplicações podem usar o mesmo VNet.
* Não é possível alterar a subscrição de uma app ou de um plano enquanto há uma aplicação que está a usar a Integração VNet regional.
* A sua aplicação não pode resolver endereços em Zonas Privadas Azure DNS sem alterações de configuração

Um endereço é usado para cada instância de plano. Se escalar a sua aplicação para cinco instâncias, então são utilizados cinco endereços. Uma vez que o tamanho da subrede não pode ser alterado após a atribuição, você deve usar uma subnet que é grande o suficiente para acomodar qualquer escala que a sua app possa alcançar. A /26 com 64 endereços é o tamanho recomendado. A /26 com 64 endereços acomoda um plano Premium com 30 instâncias. Quando escala um plano para cima ou para baixo, precisa do dobro dos endereços por um curto período de tempo.

Se quiser que as suas aplicações noutro plano cheguem a um VNet já ligado por aplicações noutro plano, selecione uma subnet diferente da utilizada pela integração vnet pré-existente.

A funcionalidade está em pré-visualização do Linux. A forma Linux da funcionalidade apenas suporta a realização de chamadas para endereços RFC 1918 (10.0.0.0.0/8, 172.16.0.0/12, 192.168.0.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Web ou App de Funções para Contentores

Se hospedar a sua aplicação no Linux com as imagens incorporadas, a VNet Integration regional funciona sem alterações adicionais. Se utilizar a Web ou a App de Função para contentores, tem de modificar a sua imagem de estivador para utilizar a Integração VNet. Na sua imagem de estivador, utilize a variável do ambiente PORT como porta de escuta do servidor web principal, em vez de utilizar um número de porta codificado. A variável ambiente PORT é automaticamente definida pela plataforma no tempo de arranque do contentor. Se utilizar o SSH, o daemon SSH deve ser configurado para ouvir o número de porta especificado pela variável ambiente SSH_PORT quando utilizar a Integração Regional vNet. Não há apoio para a integração vnet exigida por gateway em Linux.

### <a name="service-endpoints"></a>Pontos finais de serviço

A Integração VNet Regional permite-lhe utilizar pontos finais de serviço. Para utilizar pontos finais de serviço com a sua aplicação, utilize a Integração VNet regional para se conectar a um VNet selecionado e, em seguida, configurar pontos finais de serviço com o serviço de destino na subnet que utilizou para a integração. Se então queria aceder a um serviço sobre pontos finais de serviço:

1. configure integração vnet regional com a sua aplicação web
1. ir para o serviço de destino e configurar pontos finais de serviço contra a subnet utilizada para integração

### <a name="network-security-groups"></a>Grupos de segurança de rede

Você pode usar grupos de segurança de rede para bloquear tráfego de entrada e saída para recursos em um VNet. Uma aplicação que utiliza a Integração VNet regional pode usar um grupo de segurança de [rede][VNETnsg] para bloquear o tráfego de saída para recursos no seu VNet ou na internet. Para bloquear o tráfego para endereços públicos, deve ter a definição de aplicação WEBSITE_VNET_ROUTE_ALL definida para 1. As regras de entrada num NSG não se aplicam à sua aplicação porque a VNet Integration afeta apenas o tráfego de saída da sua aplicação.

Para controlar o tráfego de entrada na sua aplicação, utilize a funcionalidade Restrições de Acesso. Um NSG aplicado à sua subnet de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL está definido para 1 e não tiver nenhuma rota que afete o tráfego de endereços públicos na sua subnet de integração, todo o seu tráfego de saída ainda está sujeito a NSGs atribuídos à sua subnet de integração. Se WEBSITE_VNET_ROUTE_ALL não estiver definido, os NSGs são aplicados apenas ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Pode utilizar tabelas de rotas para direcionar o tráfego de saída da sua app para onde quiser. Por padrão, as tabelas de rotas apenas afetam o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL para 1, todas as suas chamadas de saída são afetadas. As rotas definidas na sua subnet de integração não afetarão as respostas aos pedidos de aplicações de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways.

Se quiser encaminhar todo o tráfego de saída no local, pode utilizar uma mesa de rota para enviar todo o tráfego de saída para o seu gateway ExpressRoute. Se fizer o tráfego de encaminhamento para um portal, certifique-se de que define rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetam o tráfego da sua aplicação. Se tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída da sua aplicação será afetado. Por predefinição, as rotas BGP afetam apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL estiver definido para 1, todo o tráfego de saída pode ser afetado pelas suas rotas de BGP.

### <a name="azure-dns-private-zones"></a>Zonas Privadas Azure DNS 

Depois de a sua aplicação se integrar com o seu VNet, utiliza o mesmo servidor DNS com o qual o seu VNet está configurado. Por padrão, a sua aplicação não funcionará com as Zonas Privadas Azure DNS. Para trabalhar com as Zonas Privadas Azure DNS, é necessário adicionar as seguintes definições de aplicação:

1. WEBSITE_DNS_SERVER com valor 168.63.129.16 
1. WEBSITE_VNET_ROUTE_ALL com valor 1

Estas definições enviarão todas as suas chamadas de saída da sua aplicação para o seu VNet, além de permitir que a sua aplicação utilize zonas privadas DoDNS Do Azure.

### <a name="private-endpoints"></a>Pontos finais privados

Se quiser fazer chamadas para [Pontos Finais Privados,][privateendpoints]então precisa de se integrar com as Zonas Privadas Do DNS Do Azure ou gerir o ponto final privado no servidor DNS utilizado pela sua aplicação. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
