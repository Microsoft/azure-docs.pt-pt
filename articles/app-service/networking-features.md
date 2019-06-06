---
title: Funcionalidades de implementação - serviço de aplicações do Azure de rede | Documentos da Microsoft
description: Como utilizar o serviço de aplicações de várias funcionalidades de rede
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 666430a11fb95871eb601b2a38eb7b97ad16119f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498953"
---
# <a name="app-service-networking-features"></a>Funcionalidades de rede do serviço de aplicações

Aplicações no serviço de aplicações do Azure podem ser implementadas de várias formas. Por predefinição, o serviço de aplicação alojadas aplicações são diretamente acessível pela internet e só podem alcançar pontos finais de internet alojado. No entanto, muitos aplicativos de cliente tem de controlar o tráfego de rede de entrada e saída. Existem vários recursos disponíveis no serviço de aplicações para satisfazer essas necessidades. O desafio é saber quais recursos devem ser utilizados para resolver um problema. Este documento destina-se para ajudar os clientes a determinar que funcionalidade deve ser utilizada com base em alguns casos de utilização de exemplo.

Existem dois tipos de implantação primário para o serviço de aplicações do Azure. Existe o serviço de público multi-inquilino, que aloja os planos de serviço de aplicações no gratuito, partilhado, básico, Standard, Premium e Premiumv2 SKUs de preços. Em seguida, é o único inquilino do App Service Environment, que hospeda os planos de serviço de aplicações isolado do SKU diretamente na sua rede Virtual do Azure (VNet). As funcionalidades que utiliza irão variar se estiver no serviço multi-inquilino ou num ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Funcionalidades de rede do serviço de aplicações de multi-inquilinos 

O serviço de aplicações do Azure é um sistema distribuído. As funções que processam pedidos HTTP/HTTPS recebidos são chamadas de front-ends. As funções que alojam a carga de trabalho do cliente são chamadas de funções de trabalho. Todas as funções numa implantação de serviço de aplicações existem numa rede multi-inquilino. Uma vez que existem muitos clientes diferentes na mesma unidade de escala do serviço de aplicações, não é possível ligar a rede de serviço de aplicações diretamente à sua rede. Em vez de ligar as redes, precisamos de recursos para lidar com os diversos aspectos da comunicação entre aplicativos. Os recursos que processam pedidos para a sua aplicação não podem ser utilizados para resolver problemas ao efetuar chamadas da sua aplicação. Da mesma forma, as funcionalidades de resolver problemas para chamadas da sua aplicação não podem ser utilizadas para resolver problemas para a sua aplicação.  

| Funcionalidades de entrada | Funcionalidades de saída |
|---------------------|-------------------|
| Aplicação atribuída endereço | Ligações Híbridas |
| Restrições de acesso | Gateway necessário integração de VNet |
| Pontos Finais de Serviço | Integração de VNet (pré-visualização) |

A menos que indicado de outra forma, todos os recursos podem ser utilizados em conjunto. Pode misturar os recursos para resolver os seus vários problemas.

## <a name="use-case-and-features"></a>Caso de utilização e funcionalidades

Para qualquer uso determinado caso, pode haver algumas formas de resolver o problema.  A funcionalidade correta a utilizar, por vezes, é devido a razões para além de apenas o caso de utilização em si. Os seguintes casos de utilização de entrada sugerem como utilizar o serviço de aplicações, funcionalidades de rede para resolver problemas em torno de controlar o tráfego que passa para a sua aplicação. 
 
| Casos de utilização de entrada | Funcionalidade |
|---------------------|-------------------|
| Suportar as necessidades SSL baseado em IP para a sua aplicação | aplicação atribuída endereço |
| Não partilhado, o endereço de entrada dedicado para a sua aplicação | aplicação atribuída endereço |
| Restringir o acesso à sua aplicação a partir de um conjunto de endereços bem definidos | Restrições de acesso |
| Expor a minha aplicação no IPs privados na minha VNet | ASE DE ILB </br> Gateway de aplicação com pontos finais de serviço |
| Restringir o acesso ao meu aplicativo a partir de recursos numa VNet | Pontos Finais de Serviço </br> ASE DE ILB |
| Expor o meu aplicativo num IP privado na minha VNet | ASE DE ILB </br> IP privado para de entrada num Gateway de aplicação com pontos finais de serviço |
| Proteger meu aplicativo com uma WAF | Gateway de aplicação + ASE de ILB </br> Gateway de aplicação com pontos finais de serviço </br> Porta da frente do Azure com restrições de acesso |
| Balancear carga de tráfego para as minhas aplicações em diferentes regiões | Porta da frente do Azure com restrições de acesso | 
| Balancear carga de tráfego na mesma região | Gateway de aplicação com pontos finais de serviço | 

Os casos de utilização de saída seguinte sugerem como utilizar o serviço de aplicações, funcionalidades de rede para resolver necessidades de acesso de saída para a sua aplicação. 

| Casos de utilização de saída | Funcionalidade |
|---------------------|-------------------|
| Aceder aos recursos numa rede Virtual na mesma região do Azure | Integração de VNet </br> ASE |
| Aceder aos recursos numa rede Virtual do Azure numa região diferente | Gateway necessário integração de VNet </br> Peering de VNet e de ASE |
| Aceder a recursos protegida com pontos finais de serviço | Integração de VNet </br> ASE |
| Aceder aos recursos numa rede privada não ligado ao Azure | Ligações Híbridas |
| Aceder a recursos em circuitos do ExpressRoute | Integração de VNet (restritos, por enquanto, endereços de RFC 1918) </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento predefinido de rede

As unidades de escala do serviço de aplicações do Azure suportam muitos clientes em cada implementação. Os planos gratuito e partilhado SKU alojam cargas de trabalho do cliente em funções de trabalho do multi-inquilinos. Básico e acima planos anfitrião cliente cargas de trabalho que são dedicadas a apenas um plano de serviço de aplicações (ASP). Se tivesse um plano de serviço de aplicações Standard, todas as aplicações nesse plano serão executado na função de trabalho mesmo. Se aumentar horizontalmente a função de trabalho, em seguida, todas as aplicações em que ASP serão replicadas numa nova função de trabalho para cada instância em seu ASP. Os trabalhadores que são utilizados para Premiumv2 são diferentes de trabalhadores utilizados para os outros planos. Cada implementação de serviço de aplicações tem um endereço IP que é utilizado para todo o tráfego de entrada para as aplicações dessa implementação do serviço de aplicações. Há contudo em qualquer lugar de 4 a 11 endereços utilizados para fazer chamadas de saída. Estes endereços são partilhados por todas as aplicações nessa implementação de serviço de aplicações. Os endereços de saída são diferentes com base nos tipos de trabalhador diferente. Isso significa que os endereços utilizados pelos gratuito, partilhado, básico, Standard e Premium ASPs são diferentes do que os endereços utilizados para chamadas de saída do ASPs Premiumv2. Se observar nas propriedades para a sua aplicação, pode ver os endereços de entrada e saídos que são utilizados pela sua aplicação. Se precisar de bloquear uma dependência com uma ACL de IP, utilize o possibleOutboundAddresses. 

![Propriedades da aplicação](media/networking-features/app-properties.png)

Serviço de aplicações tem um número de pontos de extremidade que são utilizados para gerir o serviço.  Esses endereços são publicados num documento separado e são também na marca de serviço AppServiceManagement IP. A etiqueta de AppServiceManagement só é utilizada com um serviço de ambiente aplicações (ASE) em que precisa para permitir esse tráfego. O serviço de aplicações, endereços de entrada são controlados na marca de serviço de IP do serviço de aplicações. Não há nenhuma marca de serviço IP que contém os endereços de saída utilizados pelo serviço de aplicações. 

![Diagrama de entrada e saído do serviço de aplicações](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Aplicação atribuída endereço 

O recurso de endereço atribuído de aplicação é um offshoot a capacidade de SSL baseado em IP e é acessado pela configuração de SSL com a sua aplicação. Esta funcionalidade pode ser utilizada para chamadas SSL baseado em IP, mas também pode ser utilizado para dar um endereço que apenas ele tem a sua aplicação. 

![Aplicação atribuída diagrama de endereço](media/networking-features/app-assigned-address.png)

Quando utiliza uma aplicação atribuída endereço, o tráfego atravessa ainda as mesmas funções de front-end que lidar com todo o tráfego de entrada para a unidade de escala do serviço de aplicações. O endereço atribuído à sua aplicação, no entanto, só é utilizado pela sua aplicação. Os casos de utilização desta funcionalidade são:

* Suportar as necessidades SSL baseado em IP para a sua aplicação
* Definir um endereço dedicado para a sua aplicação que não é partilhado com qualquer outra coisa

Pode saber como definir um endereço na sua aplicação com o tutorial [SSL baseado em IP configurando][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrições de acesso 

As restrições de acesso capacidade permite-lhe filtrar **entrada** pedidos com base no endereço IP de origem. A ação de filtragem ocorre nas funções de front-end que estão a montante de rolls a função de trabalho onde as suas aplicações são executadas. Uma vez que as funções de front-end são a montante de operadores de, a capacidade de restrições de acesso pode ser considerada como proteção ao nível da rede para as suas aplicações. A funcionalidade permite-lhe criar uma lista de permissão e negação blocos de endereços que são avaliados por ordem de prioridade. Ele é semelhante ao recurso do grupo de segurança de rede (NSG) que existe no sistema de rede do Azure.  Pode utilizar esta funcionalidade num ASE ou no serviço multi-inquilino. Quando usado com um ASE de ILB, pode restringir o acesso dos blocos de endereço privado.

![Restrições de acesso](media/networking-features/access-restrictions.png)

A funcionalidade de restrições de acesso ajuda a cenários em que pretende restringir os endereços IP que podem servir-se à sua aplicação. Entre a utilização casos para esta funcionalidade são:

* Restringir o acesso à sua aplicação a partir de um conjunto de endereços bem definidos 
* Restringir o acesso ao vem de um serviço de balanceamento de carga, como a porta da frente do Azure. Se quiser bloquear o tráfego de entrada para porta da frente do Azure, criar regras para permitir o tráfego 147.243.0.0/16 e 2a01:111:2050:: / 44. 

![Restrições de acesso com a porta de entrada](media/networking-features/access-restrictions-afd.png)

Se desejar bloquear o acesso à sua aplicação para que apenas pode ser contactado a partir de recursos na sua rede Virtual do Azure (VNet), terá um endereço estático público na sua origem na sua VNet. Se os recursos não tem um endereço público, deve usar o recurso de pontos finais de serviço em vez disso. Saiba como ativar esta funcionalidade com o tutorial num [configurar restrições de acesso][iprestrictions].

### <a name="service-endpoints"></a>Pontos finais de serviço

Pontos finais de serviço permite-lhe bloquear **entrada** aceder à sua aplicação, de modo a que o endereço de origem tem de ser um conjunto de sub-redes que selecionou. Esta funcionalidade funciona em conjunto com as restrições de acesso de IP. Pontos finais de serviço estão definidos na mesma experiência de utilizador que as restrições de acesso de IP. Pode criar uma Permitir/Negar lista de regras de acesso que inclui endereços públicos, bem como sub-redes nas suas VNets. Esta funcionalidade suporta cenários tais como:

![pontos finais de serviço](media/networking-features/service-endpoints.png)

* Configurar um Gateway de aplicação com a sua aplicação para bloquear o tráfego de entrada para a sua aplicação
* Testricting acesso à sua aplicação para recursos na sua VNet. Isto pode incluir as VMs, os ASEs ou, inclusivamente, noutras aplicações que utilizam a integração de VNet 

![pontos finais de serviço com o gateway de aplicação](media/networking-features/service-endpoints-appgw.png)

Pode saber mais sobre como configurar pontos finais de serviço com a sua aplicação no tutorial num [configurar restrições de acesso de ponto final de serviço][serviceendpoints]
 
### <a name="hybrid-connections"></a>Ligações Híbridas

Ligações híbridas do serviço de aplicações permite às aplicações tornar **saída** chamadas para pontos finais TCP especificados. O ponto final pode ser no local, numa VNet ou em qualquer lugar que permite tráfego de saída para o Azure na porta 443. A funcionalidade requer a instalação de um agente de retransmissão chamado o Gestor de ligação híbrida (HCM) num Windows Server 2012 ou o anfitrião mais recente. O HCM precisa ser capaz de alcançar o reencaminhamento do Azure na porta 443. O HCM pode ser baixado da IU da aplicação serviço híbrida ligações no portal. 

![Fluxo de rede de ligações híbridas](media/networking-features/hybrid-connections.png)

A funcionalidade de ligações de híbridas do serviço de aplicação baseia-se a capacidade de ligações de híbridas de reencaminhamento do Azure. Serviço de aplicações utiliza um formulário especializado da funcionalidade que suporta apenas fazendo chamadas de saída da sua aplicação para um anfitrião TCP e a porta. Esta porta e anfitrião apenas tem de resolver no anfitrião onde o HCM está instalado. Quando a aplicação, serviço de aplicações, faz uma pesquisa de DNS no anfitrião e porta definidos na sua ligação híbrida, o tráfego é automaticamente redirecionado através da ligação híbrida e o Gestor de ligações híbridas. Para saber mais sobre as ligações híbridas, leia a documentação sobre [ligações híbridas do serviço de aplicações][hybridconn]

Esta funcionalidade é frequentemente utilizada para:

* Aceder a recursos em redes privadas que não estiver ligados ao Azure com uma VPN ou ExpressRoute
* Suporta a migração lift- and -shift de aplicações no local para o serviço de aplicações sem a necessidade de mover também o suporte de bases de dados  
* Fornece com segurança o acesso a um único host e uma porta por ligação híbrida. A maioria das funcionalidades de rede abre o acesso a uma rede e ligações híbridas tem apenas o único host e a porta que pode entrar.
* Abrangem os cenários não abrangidos por outros métodos de conectividade de saída
* Realizar o desenvolvimento no serviço de aplicações em que as aplicações podem facilmente aproveitar recursos no local 

Uma vez que o recurso permite o acesso a recursos no local sem uma brecha de firewall de entrada, é popular entre os desenvolvedores. Os outros saídos serviço de aplicações recursos de rede são muito Virtual rede Azure relacionados. As ligações híbridas não tem uma dependência de passar por uma VNet e podem ser utilizadas para uma maior variedade de necessidades de rede. É importante observar que a funcionalidade de ligações de híbridas do serviço de aplicações não se importa com nem sabe o que está a fazer com base no mesmo. Isto é, digamos que pode usá-lo para aceder a uma base de dados, um serviço web ou um soquete TCP arbitrário no seu próprio mainframe. A funcionalidade de túneis essencialmente pacotes TCP. 

Enquanto as ligações híbridas são populares para desenvolvimento, também é utilizado em várias aplicações de produção também. É ótimo para aceder a um serviço web ou a base de dados, mas não é adequada para situações que envolvem um ligações muitos excelentes que está sendo criado. 

### <a name="gateway-required-vnet-integration"></a>Gateway necessário integração de VNet 

O gateway necessária a funcionalidade de integração de VNet de serviço de aplicações permite à sua aplicação tornar **saída** pedidos numa rede Virtual do Azure. A funcionalidade funciona ao ligar-se o host a que sua aplicação está em execução gateway de rede Virtual na sua VNet com uma VPN ponto a site. Quando configurar a funcionalidade, a aplicação obtenha um dos endereços de ponto a site atribuídos para cada instância. Esta funcionalidade permite-lhe aceder a recursos na clássico ou VNets do Resource Manager em qualquer região. 

![Gateway necessário integração de VNet](media/networking-features/gw-vnet-integration.png)

Esta funcionalidade resolve o problema de acesso a recursos em outras VNets e até mesmo pode ser utilizada para ligar através de uma VNet a outras VNets ou até mesmo no local. Não funciona com o ExpressRoute ligado VNets mas não com VPN Site a site ligados a redes. É normalmente inadequado utilizar esta funcionalidade de uma aplicação num serviço de ambiente aplicações (ASE), porque o ASE já se encontra na sua VNet. Os casos de utilização que resolve esta funcionalidade são:

* Aceder a recursos na IPs privados nas suas redes virtuais do Azure 
* Aceder a recursos no local se houver uma VPN de site a site 
* Aceder a recursos em VNets em modo de peering 

Quando esta funcionalidade está ativada, a aplicação irá utilizar o servidor DNS configurado com o VNet de destino. Pode ler mais sobre esta funcionalidade na documentação no [integração de VNet de serviço de aplicações][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integração de VNet

O gateway necessária a funcionalidade de integração de VNet é muito útil, mas ainda não resolve o recursos ao aceder por ExpressRoute. Com base no que precisam de aceder através de ligações do ExpressRoute, há uma necessidade para aplicações poder fazer chamadas para serviços protegidos por ponto final de serviço. Para resolver ambos essas necessidades adicionais, outro recurso de integração de VNet foi adicionado. A nova funcionalidade de integração de VNet permite-lhe colocar o back-end da sua aplicação numa sub-rede numa VNet do Resource Manager na mesma região. Esta funcionalidade não está disponível a partir de um ambiente de serviço de aplicações, que já se encontra numa VNet. Esta funcionalidade permite:

* Aceder a recursos em VNets do Resource Manager na mesma região
* Aceder a recursos que estão protegidos por pontos finais de serviço 
* Aceder a recursos que são acessíveis através de ligações de VPN ou ExpressRoute

![Integração de VNet](media/networking-features/vnet-integration.png)

Esta funcionalidade está em pré-visualização e não deve ser utilizada para cargas de trabalho de produção. Para saber mais sobre esta funcionalidade, continue a ler os documentos [integração de VNet de serviço de aplicações][vnetintegration].

## <a name="app-service-environment"></a>Ambiente do Serviço de Aplicações 

Um ambiente de serviço de aplicações (ASE) é uma implementação de inquilino único do serviço de aplicações do Azure que é executado na sua VNet. O ASE permite que os casos de utilização, tais como:

* Aceder a recursos na sua VNet
* Aceder aos recursos através do ExpressRoute
* Exponha as suas aplicações com um endereço privado da vnet 
* Aceder a recursos nos pontos finais de serviço 

Com um ASE, não é necessário utilizar funcionalidades como a integração de VNet ou pontos finais de serviço, porque o ASE já se encontra na sua VNet. Se pretender aceder a recursos como SQL ou o armazenamento através de pontos finais de serviço, ative pontos finais de serviço na sub-rede do ASE. Se pretender aceder aos recursos na VNet, não é necessária nenhuma configuração adicional.  Se pretender aceder aos recursos através do ExpressRoute, que já está na VNet e não é necessário configurar nada o ASE ou as aplicações no interior do mesmo. 

Uma vez que as aplicações num ASE de ILB podem ser expostas num endereço IP privado, pode facilmente adicionar dispositivos de WAF para expor apenas as aplicações que pretende para a internet e mantenha o resto seguro. Ele é adaptado para um desenvolvimento fácil de aplicações de várias camadas. 

Existem algumas coisas que ainda não são possíveis a partir do serviço de multi-inquilino que são de um ASE. Por exemplo coisas como:

* Expor seus aplicativos num endereço IP privado
* Proteger todo o tráfego de saída com controlos de rede que não fazem parte da sua aplicação 
* Alojar as suas aplicações no serviço de um único inquilino 
* Aumentar verticalmente para várias instâncias de mais do que são possíveis no serviço multi-inquilino 
* Carregar certificados de cliente de AC privados para utilização pelas suas aplicações com a autoridade de certificação privada protegida por pontos de extremidade 
* Impor TLS 1.1 em todas as aplicações alojadas no sistema sem qualquer capacidade de desativar ao nível da aplicação 
* Forneça um endereço de saída dedicado para todas as aplicações no ASE que não é partilhado com quaisquer clientes 

![ASE numa VNet](media/networking-features/app-service-environment.png)

O ASE fornece a história da melhor forma em torno de alojamento de aplicações isolada e dedicada, mas apresenta alguns desafios de gestão. Alguns aspetos a considerar antes de utilizar um ASE operacional são:
 
 * Um ASE é executado na sua VNet, mas tem dependências fora da VNet. Essas dependências têm de ser permitidas. Leia mais em [considerações de rede para um ambiente de serviço de aplicações][networkinfo]
 * Um ASE dimensiona imediatamente, como o serviço de multi-inquilino. Tem de antecipar as necessidades de dimensionamento, em vez de dimensionar de forma reativa. 
 * Um ASE tem um maior com antecedência custo associado ao mesmo. Para obter o máximo proveito do seu ASE, deve planejar colocar muitas cargas de trabalho num ASE em vez de tê-lo utilizado para os esforços de pequenas
 * As aplicações num ASE não é possível restringir o acesso a algumas aplicações no ASE e não para outros.
 * O ASE está numa sub-rede e quaisquer regras de rede são aplicadas a todo o tráfego de e para o ASE. Se pretender atribuir regras de tráfego de entrada para apenas uma aplicação, utilize restrições de acesso. 

## <a name="combining-features"></a>A combinação de funcionalidades 

Os recursos indicados para o serviço de multi-inquilino podem ser usados juntos para resolver mais elaborados casos de utilização. Dois dos casos de utilização mais comuns são descritas aqui, mas eles são apenas exemplos. Ao compreender o que fazem os vários recursos, pode resolver praticamente todas as suas necessidades de arquitetura do sistema.

### <a name="inject-app-into-a-vnet"></a>Injetar aplicação uma VNet

Uma solicitação comum é sobre como colocar a sua aplicação numa VNet. Colocar a sua aplicação numa VNet significa que os pontos finais de entrada e saídos para uma aplicação numa VNet. O ASE fornece a melhor solução para resolver este problema, mas pode aproveitar o que é necessário no serviço multi-inquilino através da combinação de funcionalidades. Por exemplo, pode alojar apenas aplicativos de intranet com endereços privados de entrada e saídos por:

* Criar um Gateway de aplicação com o endereço privado de entrada e saído
* Proteger o tráfego de entrada à sua aplicação com pontos finais de serviço 
* Utilizar a nova integração de VNet para que o back-end da sua aplicação está na sua VNet 

Esse estilo de implementação não seria dão-lhe um endereço dedicado para o tráfego de saída à internet ou dão-lhe a capacidade de bloquear todo o tráfego de saída da sua aplicação.  Esse estilo de implementação daria um muito do que apenas caso contrário, obteria com um ASE. 

### <a name="create-multi-tier-applications"></a>Criar aplicações de várias camadas

Um aplicativo de várias camado é uma aplicação em que as aplicações de back-end de API só podem ser acedidas da camada de front-end. Para criar uma aplicação de várias camadas, pode:

* Utilizar a integração de VNet para ligar o back-end da sua aplicação web de front-end com uma sub-rede numa VNet
* Utilizar pontos finais de serviço para proteger o tráfego de entrada para a aplicação API para apenas disponível na sub-rede utilizada pela sua aplicação web front-end

![aplicação de várias camada](media/networking-features/multi-tier-app.png)

Pode ter várias aplicações de front-end utilizar a mesma aplicação de API com integração de VNet a partir de outras aplicações de front-end e pontos finais de serviço a partir da aplicação de API com suas sub-redes.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
