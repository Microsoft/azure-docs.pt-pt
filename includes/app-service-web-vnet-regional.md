---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671544"
---
A utilização da Integração VNet regional permite ao acesso da sua aplicação:

* recursos na VNet na mesma região com que se integra 
* recursos em VNets olhou para o seu VNet que estão na mesma região
* serviço endpoint serviços seguros
* recursos através das ligações ExpressRoute
* recursos na VNet a que está ligado
* recursos através de conexões peered, incluindo conexões ExpressRoute
* pontos finais privados 

Ao utilizar a Integração VNet com VNets na mesma região, pode utilizar as seguintes funcionalidades de Networking Azure:

* Grupos de Segurança da Rede (NSGs) - Pode bloquear o tráfego de saída com um Grupo de Segurança de Rede que é colocado na sua subnet de integração. As regras de entrada não se aplicam, uma vez que não pode utilizar a Integração VNet para fornecer acesso de entrada à sua aplicação.
* Tabelas de Rotas (UDRs) - Pode colocar uma tabela de rotas na subnet de integração para enviar tráfego de saída onde quiser. 

Por padrão, a sua aplicação apenas irá direcionar o tráfego RFC1918 para o seu VNet. Se quiser encaminhar todo o tráfego de saída para o seu VNet, aplique a definição de aplicação WEBSITE_VNET_ROUTE_ALL na sua aplicação. Para configurar a definição da aplicação:

1. Vá ao UI de Configuração no portal da sua aplicação. Selecione **nova definição de aplicação**
1. Digite **WEBSITE_VNET_ROUTE_ALL** no campo Nome e **1** no campo Valor

   ![Fornecer a definição de aplicações][4]

1. Selecione **OK**
1. Selecione **Guardar**

Se encaminhar todo o tráfego de saída para o seu VNet, então estará sujeito aos NSGs e UDRs que são aplicados à sua subnet de integração. Quando encaminha todo o seu tráfego de saída para o seu VNet, os seus endereços de saída continuarão a ser os endereços de saída que estão listados nas propriedades da sua aplicação, a menos que forneça rotas para enviar o tráfego para outro lugar. 

Existem algumas limitações na utilização da Integração VNet com VNets na mesma região:

* Não é possível alcançar recursos através de ligações globais de observação
* A funcionalidade só está disponível a partir de unidades de escala mais recentes do App Service que suportam planos de Serviço de Aplicações PremiumV2.
* A subnet de integração só pode ser utilizada por um plano de Serviço de Aplicações
* A funcionalidade não pode ser utilizada por aplicações de plano isoladoque se encontram num Ambiente de Serviço de Aplicações
* A funcionalidade requer uma sub-rede não utilizada que seja uma /27 com 32 endereços ou maior num VNet do Gestor de Recursos
* A aplicação e o VNet devem estar na mesma região
* Não é possível eliminar um VNet com uma aplicação integrada. Retire a integração antes de eliminar o VNet. 
* Só pode integrar-se com VNets na mesma subscrição que a app
* Você pode ter apenas uma Integração VNet regional por plano de serviço de aplicações. Várias aplicações no mesmo plano de Serviço de Aplicações podem usar o mesmo VNet. 
* Não é possível alterar a subscrição de uma app ou de um plano enquanto existe uma aplicação que está a utilizar a Integração Regional VNet

Um endereço é usado para cada instância de plano. Se escalar a sua aplicação para cinco instâncias, então são utilizados cinco endereços. Uma vez que o tamanho da subrede não pode ser alterado após a atribuição, deve utilizar uma sub-rede suficientemente grande para acomodar qualquer escala que a sua aplicação possa alcançar. A /26 com 64 endereços é o tamanho recomendado. A /26 com 64 endereços acomodará um plano Premium com 30 instâncias. Quando escala um plano para cima ou para baixo, precisa do dobro dos endereços por um curto período de tempo. 

Se quiser que as suas aplicações noutro plano cheguem a um VNet que já esteja ligado por aplicações noutro plano, precisa de selecionar uma sub-rede diferente da utilizada pela Integração VNet pré-existente.  

A funcionalidade está em pré-visualização do Linux. A forma Linux da funcionalidade apenas suporta a realização de chamadas para endereços RFC 1918 (10.0.0.0.0/8, 172.16.0.0/12, 192.168.0.0.0/16).

### <a name="web--function-app-for-containers"></a>Web / App de funções para contentores

Se hospedar a sua aplicação no Linux com as imagens incorporadas, a VNet Integration regional funciona sem alterações adicionais. Se utilizar a Aplicação Web /Função para Contentores, tem de modificar a sua imagem de estivador para utilizar a Integração VNet. Na sua imagem de estivador, utilize a variável do ambiente PORT como porta de escuta do servidor web principal, em vez de utilizar um número de porta codificado. A variável ambiente PORT é automaticamente definida pela plataforma no tempo de arranque do contentor. Se estiver a utilizar o SSH, o daemon SSH deve ser configurado para ouvir o número de porta especificado pela variável ambiente SSH_PORT ao utilizar a integração regional de VNet.  Não há suporte para a integração vnet necessária em Linux. 

### <a name="service-endpoints"></a>Pontos Finais de Serviço

A Integração VNet Regional permite-lhe utilizar pontos finais de serviço.  Para utilizar pontos finais de serviço com a sua aplicação, utilize a Integração VNet regional para se ligar a um VNet selecionado e, em seguida, configurar pontos finais de serviço na subnet que utilizou para a integração. 

### <a name="network-security-groups"></a>Grupos de Segurança de Rede

Os Grupos de Segurança da Rede permitem bloquear o tráfego de entrada e saída para recursos num VNet. Uma aplicação que utiliza a Integração VNet regional pode usar o [Network Security Group][VNETnsg] para bloquear o tráfego de saída para recursos no seu VNet ou na internet. Para bloquear o tráfego para endereços públicos, deve ter a definição de aplicação WEBSITE_VNET_ROUTE_ALL definida para 1. As regras de entrada num NSG não se aplicam à sua aplicação, uma vez que a Integração VNet apenas afeta o tráfego de saída da sua aplicação. Para controlar o tráfego de entrada na sua aplicação, utilize a funcionalidade Restrições de Acesso. Um NSG aplicado à sua subnet de integração estará em vigor independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL estivesse definido para 1 e não tivesse nenhuma rota que afetasse o tráfego de endereços públicos na sua subnet de integração, todo o seu tráfego de saída ainda estaria sujeito a NSGs atribuídos à sua subnet de integração. Se WEBSITE_VNET_ROUTE_ALL não fosse definida, os NSGs só seriam aplicados ao tráfego RFC1918.

### <a name="routes"></a>Rotas

As Route Tables permitem-lhe desviar o tráfego de saída da sua app para onde quiser. Por padrão, as tabelas de rotas apenas afetarão o tráfego de destino RFC1918.  Se WEBSITE_VNET_ROUTE_ALL para 1, todas as suas chamadas de saída serão afetadas. As rotas definidas na sua subnet de integração não afetarão as respostas aos pedidos de aplicações de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways. Se quiser encaminhar todo o tráfego de saída no local, pode utilizar uma mesa de rota para enviar todo o tráfego de saída para o seu gateway ExpressRoute. Se fizer o tráfego de encaminhamento para um portal, certifique-se de que define rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetarão o tráfego da sua aplicação. Se tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída da sua aplicação será afetado. Por predefinição, as rotas BGP apenas afetarão o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL está definido para 1, todo o tráfego de saída pode ser afetado pelas suas rotas de BGP. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/