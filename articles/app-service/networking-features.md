---
title: Funcionalidades de rede
description: Conheça as funcionalidades da rede no Azure App Service e quais as funcionalidades que necessita para as necessidades da sua rede para segurança ou funcionalidade.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: af4c333fb539ad533756c538cb3ecde1d9a91413
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743051"
---
# <a name="app-service-networking-features"></a>Funcionalidades de networking do Serviço de Aplicações

As aplicações no Azure App Service podem ser implementadas de várias maneiras. Por padrão, as aplicações hospedadas no Serviço de Aplicações são diretamente acessíveis à Internet e só podem chegar aos pontos finais hospedados na Internet. Muitas aplicações de clientes precisam, no entanto, de controlar o tráfego de rede de entrada e saída. Existem várias funcionalidades disponíveis no Serviço de Aplicações para satisfazer essas necessidades. O desafio é saber que característica deve ser usada para resolver um determinado problema. Este documento destina-se a ajudar os clientes a determinar que funcionalidade deve ser utilizada com base em alguns casos de utilização de exemplo.

Existem dois tipos de implementação primária para o Serviço de Aplicações Azure. Existe o serviço público multi-inquilino, que acolhe planos de Serviço de Aplicações nos SKUs de preços gratuitos, partilhados, básicos, padrão, premium, PremiumV2 e PremiumV3. Depois há o único inquilino App Service Environment (ASE), que acolhe planos isolados do SKU App Service diretamente na sua Rede Virtual Azure (VNet). As funcionalidades que utiliza variarão se estiver no serviço multi-inquilino ou num ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Recursos de rede multi-inquilinos do Serviço de Aplicações 

O Azure App Service é um sistema distribuído. As funções que lidam com pedidos HTTP/HTTPS são chamadas front-ends. Os papéis que acolhem a carga de trabalho do cliente são chamados de trabalhadores. Todos os papéis numa implementação do Serviço de Aplicações existem numa rede multi-inquilinos. Como existem muitos clientes diferentes na mesma unidade de escala de Serviço de Aplicações, não é possível ligar a rede de Serviço de Aplicações diretamente à sua rede. Em vez de ligar as redes, precisamos de funcionalidades para lidar com os diferentes aspetos da comunicação de aplicações. As funcionalidades que lidam com pedidos DA sua aplicação não podem ser usadas para resolver problemas ao fazer chamadas da sua aplicação. Da mesma forma, as funcionalidades que resolvem problemas para chamadas da sua aplicação não podem ser usadas para resolver problemas na sua app.  

| Características de entrada | Características de saída |
|---------------------|-------------------|
| Endereço atribuído à app | Ligações Híbridas |
| Restrições de acesso | Gateway exigia integração VNet |
| Pontos Finais de Serviço | Integração de VNet |

Salvo indicação em contrário, todas as funcionalidades podem ser utilizadas em conjunto. Pode misturar as funcionalidades para resolver os seus vários problemas.

## <a name="use-case-and-features"></a>Use estojo e funcionalidades

Para qualquer caso de uso, pode haver algumas maneiras de resolver o problema.  A função certa a utilizar deve-se, por vezes, a razões para além do próprio caso de utilização. Os seguintes casos de utilização de entrada sugerem como usar as funcionalidades de rede do Serviço de Aplicações para resolver problemas em torno do controlo do tráfego que vai para a sua aplicação. 
 
| Casos de utilização de entrada | Funcionalidade |
|---------------------|-------------------|
| Suporte necessidades SSL baseadas em IP para a sua aplicação | endereço atribuído a aplicativo |
| Endereço de entrada não partilhado e dedicado para a sua aplicação | endereço atribuído a aplicativo |
| Restringir o acesso à sua aplicação a partir de um conjunto de endereços bem definidos | Restrições de acesso |
| Restringir o acesso à minha app a partir de recursos numa VNet | Pontos Finais de Serviço </br> ILB ASE </br> Ponto final privado (Pré-visualização) |
| Expor a minha aplicação num IP privado no meu VNet | ILB ASE </br> IP privado para entrada em um Gateway de aplicação com pontos finais de serviço </br> Ponto final de serviço (pré-visualização) |
| Proteja a minha app com um WAF | Gateway de aplicação + ILB ASE </br> Gateway de Aplicação com pontos finais de serviço </br> Porta frontal Azure com restrições de acesso |
| Carregar o tráfego de equilíbrio para as minhas apps em diferentes regiões | Porta frontal Azure com restrições de acesso | 
| Tráfego de balanço de carga na mesma região | [Gateway de Aplicação com pontos finais de serviço][appgwserviceendpoints] | 

Os seguintes casos de utilização de saída sugerem como usar as funcionalidades de rede do Serviço de Aplicações para resolver as necessidades de acesso de saída para a sua aplicação. 

| Casos de utilização de saída | Funcionalidade |
|---------------------|-------------------|
| Aceder a recursos numa Rede Virtual Azure na mesma região | Integração de VNet </br> ASE |
| Aceder a recursos numa Rede Virtual Azure numa região diferente | Gateway exigia integração VNet </br> Ase e VNet espreitando |
| Recursos de acesso protegidos com pontos finais de serviço | Integração de VNet </br> ASE |
| Recursos de acesso numa rede privada não ligada ao Azure | Ligações Híbridas |
| Recursos de acesso em todos os circuitos ExpressRoute | Integração de VNet </br> ASE | 
| Tráfego de saída seguro a partir da sua aplicação web | Grupos de Integração VNet e Segurança de Rede </br> ASE | 
| Encaminhe o tráfego de saída a partir da sua aplicação web | Tabelas de Integração e Rota VNet </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento de rede padrão

As unidades de escala de serviço de aplicações Azure suportam muitos clientes em cada implementação. Os planos SKU gratuitos e partilhados acolhem cargas de trabalho dos clientes em trabalhadores multi-inquilinos. O Basic, e acima de planos, acolhe cargas de trabalho de clientes que são dedicadas a apenas um plano de Serviço de Aplicações (ASP). Se tiver um plano standard de Serviço de Aplicações, todas as aplicações nesse plano serão executadas com o mesmo trabalhador. Se escaloná-lo, todas as aplicações dessa ASP serão replicadas num novo trabalhador para cada instância no seu ASP. Os trabalhadores que são utilizados para o PremiumV2 e o PremiumV3 são diferentes dos trabalhadores utilizados para os outros planos. Cada implementação do Serviço de Aplicações tem um endereço IP que é utilizado para todo o tráfego de entrada para as aplicações nessa implementação do Serviço de Aplicações. No entanto, existem entre 4 e 11 endereços utilizados para fazer chamadas de saída. Estes endereços são partilhados por todas as aplicações que são implantações do Serviço de Aplicações. Os endereços de saída são diferentes com base nos diferentes tipos de trabalhadores. Isto significa que os endereços utilizados pelos ASPs gratuitos, partilhados, básicos, standard e premium são diferentes dos endereços utilizados para chamadas de saída dos ASPs PremiumV2 e PremiumV3. Se procurar nas propriedades da sua aplicação, pode ver os endereços de entrada e saída que são utilizados pela sua aplicação. Se precisar de bloquear uma dependência com um IP ACL, utilize os possíveis OutboundAddresses. 

![Propriedades de aplicativos](media/networking-features/app-properties.png)

O Serviço de Aplicações tem uma série de pontos finais que são usados para gerir o serviço.  Estes endereços são publicados num documento separado e também estão na etiqueta de serviço IP appServiceManagement. A etiqueta AppServiceManagement é utilizada apenas com um Ambiente de Serviço de Aplicações (ASE) onde é necessário permitir esse tráfego. Os endereços de entrada do Serviço de Aplicações são rastreados na etiqueta de serviço IP do AppService. Não existe uma etiqueta de serviço IP que contenha os endereços de saída utilizados pelo Serviço de Aplicações. 

![Diagrama de entrada e saída do Serviço de Aplicações](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Endereço atribuído à app 

A funcionalidade de endereço atribuída à aplicação é um offshoot da capacidade SSL baseada em IP e é acedida através da criação de SSL com a sua aplicação. Esta funcionalidade pode ser utilizada para chamadas SSL baseadas em IP, mas também pode ser usada para dar à sua aplicação um endereço que só ele possui. 

![Diagrama de endereço atribuído app](media/networking-features/app-assigned-address.png)

Quando utiliza um endereço atribuído a uma aplicação, o seu tráfego ainda passa pelas mesmas funções frontais que manuseiam todo o tráfego de entrada na unidade de escala de Serviço de Aplicações. O endereço que é atribuído à sua app, no entanto, é utilizado apenas pela sua aplicação. Os casos de utilização desta funcionalidade são:

* Suporte necessidades SSL baseadas em IP para a sua aplicação
* Desaprova um endereço dedicado para a sua app que não seja partilhado com mais nada

Pode aprender a definir um endereço na sua aplicação com o tutorial no [Add a TLS/SSL certificate in Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrições de acesso 

A capacidade de Restrições de Acesso permite filtrar pedidos **de entrada** com base no endereço IP originário. A ação de filtragem ocorre nas funções frontais que estão a montante das funções de trabalhador onde as suas aplicações estão a decorrer. Uma vez que as funções frontais são a montante dos trabalhadores, a capacidade de Restrições de Acesso pode ser considerada como proteção ao nível da rede para as suas apps. A funcionalidade permite-lhe construir uma lista de blocos de endereços de permitir e negar que são avaliados por ordem prioritária. É semelhante à funcionalidade Network Security Group (NSG) que existe no Azure Networking.  Você pode usar esta funcionalidade em um ASE ou no serviço multi-inquilino. Quando usado com um ILB ASE, pode restringir o acesso a partir de blocos de endereços privados.

![Restrições de acesso](media/networking-features/access-restrictions.png)

A funcionalidade Restrições de Acesso ajuda em cenários onde pretende restringir os endereços IP que podem ser usados para chegar à sua aplicação. Entre os casos de utilização para esta funcionalidade estão:

* Restringir o acesso à sua aplicação a partir de um conjunto de endereços bem definidos 
* Restringir o acesso a um serviço de equilíbrio de carga, como a Porta frontal Azure. Se quiser bloquear o tráfego de entrada até a Porta frontal Azure, crie regras para permitir o tráfego a partir das 147.243.0.0/16 e 2a01:111:2050::/44. 

![Restrições de acesso com porta da frente](media/networking-features/access-restrictions-afd.png)

Se pretender bloquear o acesso à sua aplicação para que ela só possa ser alcançada a partir de recursos na sua Rede Virtual Azure (VNet), precisa de um endereço público estático em qualquer que seja a sua fonte no seu VNet. Se os recursos não tiverem uma morada pública, deverá utilizar a funcionalidade 'Endpoints' de serviço. Saiba como ativar esta funcionalidade com o tutorial sobre [restrições de acesso configuradas.][iprestrictions]

### <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos finais de serviço permitem-lhe bloquear o **acesso** à sua aplicação de modo a que o endereço de origem tenha vindo de um conjunto de sub-redes que seleciona. Esta funcionalidade funciona em conjunto com as Restrições de Acesso IP. Os pontos finais de serviço são definidos na mesma experiência do utilizador que as Restrições de Acesso IP. Pode construir uma lista de regras de acesso que incluem endereços públicos, bem como sub-redes nos seus VNets. Esta funcionalidade suporta cenários como:

![pontos finais de serviço](media/networking-features/service-endpoints.png)

* Configurar um Gateway de Aplicações com a sua app para bloquear o tráfego de entrada na sua app
* Restringindo o acesso à sua app a recursos no seu VNet. Isto pode incluir VMs, ASEs ou até mesmo outras aplicações que usam a Integração VNet 

![pontos finais de serviço com gateway de aplicação](media/networking-features/service-endpoints-appgw.png)

Pode saber mais sobre configurar pontos finais de serviço com a sua aplicação no tutorial sobre [restrições de acesso ao ponto final do serviço de configuração][serviceendpoints]

### <a name="private-endpoint-preview"></a>Ponto final privado (pré-visualização)

Private Endpoint é uma interface de rede que o liga de forma privada e segura à sua Web App por Azure Private Link. O Private Endpoint utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo a Web App para o seu VNet. Esta funcionalidade **destina-se** apenas aos fluxos de entrada para a sua Web App.
[Utilização de pontos finais privados para app Web Azure (pré-visualização)][privateendpoints]
 
### <a name="hybrid-connections"></a>Ligações Híbridas

As Conexões Híbridas do Serviço de Aplicações permitem que as suas aplicações façam chamadas **de saída** para pontos finais TCP especificados. O ponto final pode ser no local, num VNet ou em qualquer lugar que permita o tráfego de saída para Azure na porta 443. A funcionalidade requer a instalação de um agente de retransmissão chamado Hybrid Connection Manager (HCM) num Windows Server 2012 ou num anfitrião mais recente. O HCM precisa de ser capaz de chegar ao Azure Relay na porta 443. O HCM pode ser descarregado a partir do UI híbrido do Serviço de Aplicações no portal. 

![Fluxo de rede de conexões híbridas](media/networking-features/hybrid-connections.png)

A funcionalidade de Conexões Híbridas do Serviço de Aplicações é construída sobre a capacidade de conexões híbridas Azure Relay. O App Service utiliza uma forma especializada da funcionalidade que apenas suporta fazer chamadas de saída da sua app para um anfitrião e porta TCP. Este hospedeiro e porta só precisam de ser resolvidos no hospedeiro onde o HCM está instalado. Quando a aplicação, no Serviço de Aplicações, faz uma procura de DNS no anfitrião e na porta definida na sua Conexão Híbrida, o tráfego é automaticamente redirecionado para passar pela Conexão Híbrida e sair do Gestor de Conexão Híbrida. Para saber mais sobre conexões híbridas, leia a documentação sobre [conexões híbridas de serviço de aplicações][hybridconn]

Esta funcionalidade é comumente utilizada para:

* Recursos de acesso em redes privadas que não estejam ligados ao Azure com uma VPN ou ExpressRoute
* Suporte elevador e mudança de apps no local para o Serviço de Aplicações sem necessidade de mover também bases de dados de suporte  
* Fornecer de forma segura acesso a um único hospedeiro e porta por Ligação Híbrida. A maioria das funcionalidades de networking tem acesso aberto a uma rede e com Conexões Híbridas só tem o único hospedeiro e porta que pode alcançar.
* Cenários de cobertura não abrangidos por outros métodos de conectividade de saída
* Realizar desenvolvimento no Serviço de Aplicações onde as aplicações podem facilmente alavancar recursos no local 

Como a funcionalidade permite o acesso a recursos no local sem um buraco de firewall de entrada, é popular entre os desenvolvedores. As outras funcionalidades de rede de serviço de aplicações de saída estão muito relacionadas com a rede virtual Azure. As Ligações Híbridas não têm dependência de passar por um VNet e podem ser usadas para uma maior variedade de necessidades de networking. É importante notar que a funcionalidade De Ligações Híbridas do Serviço de Aplicações não se importa nem sabe o que está a fazer em cima dela. Ou seja, pode usá-lo para aceder a uma base de dados, a um serviço web ou a uma tomada TCP arbitrária num computador central. A característica essencialmente túneis pacotes TCP. 

Embora a Hybrid Connections seja popular para o desenvolvimento, também é usada em numerosas aplicações de produção. É ótimo para aceder a um serviço web ou base de dados, mas não é apropriado para situações que envolvam a criação de muitas ligações. 

### <a name="gateway-required-vnet-integration"></a>Gateway exigia integração VNet 

O gateway requer a funcionalidade de Integração VNet do Serviço de Aplicações que permite à sua aplicação fazer pedidos **de saída** numa Rede Virtual Azure. A funcionalidade funciona ligando o anfitrião a sua aplicação está a correr para um gateway de Rede Virtual no seu VNet com uma VPN ponto-a-local. Ao configurar a funcionalidade, a sua aplicação obtém um dos endereços ponto a local atribuídos a cada instância. Esta funcionalidade permite-lhe aceder a recursos em VNets Clássicos ou Gestores de Recursos em qualquer região. 

![Gateway exigia integração VNet](media/networking-features/gw-vnet-integration.png)

Esta funcionalidade resolve o problema de aceder a recursos em outros VNets e pode mesmo ser usada para ligar através de um VNet a outros VNets ou mesmo no local. Não funciona com VNets conectados expressRoute, mas funciona com redes VPN ligadas site-to-site. Normalmente, é inadequado utilizar esta funcionalidade a partir de uma aplicação num Ambiente de Serviço de Aplicações (ASE), porque o ASE já se encontra no seu VNet. Os casos de utilização que esta funcionalidade resolve são:

* Aceder a recursos em IPs privados nas suas redes virtuais Azure 
* Aceder aos recursos no local se houver uma VPN site-to-site 
* Aceder a recursos em VNets espreitados 

Quando esta funcionalidade estiver ativada, a sua aplicação utilizará o servidor DNS com o qual o VNet de destino está configurado. Pode ler mais sobre esta funcionalidade na documentação sobre a [Integração VNet do Serviço de Aplicações.][vnetintegrationp2s] 

### <a name="vnet-integration"></a>Integração de VNet

A funcionalidade de Integração VNet exigida pelo gateway é muito útil, mas ainda não resolve o acesso aos recursos através do ExpressRoute. Além da necessidade de chegar a todas as ligações ExpressRoute, existe a necessidade de as aplicações poderem fazer chamadas para serviços de serviço seguros. Para resolver ambas as necessidades adicionais, foi adicionada outra capacidade de Integração VNet. A nova funcionalidade de Integração VNet permite-lhe colocar o backend da sua aplicação numa sub-rede numa VNet do Gestor de Recursos na mesma região. Esta funcionalidade não está disponível num Ambiente de Serviço de Aplicações, que já se encontra num VNet. Esta funcionalidade permite:

* Acesso a recursos em VNets gestores de recursos na mesma região
* Aceder a recursos que são garantidos com pontos finais de serviço 
* Aceder a recursos acessíveis através das ligações ExpressRoute ou VPN
* Assegurar todo o tráfego de saída 
* Força a fazer um túnel em todo o tráfego de saída. 

![Integração de VNet](media/networking-features/vnet-integration.png)

Para saber mais sobre esta funcionalidade, leia os docs sobre [a Integração VNet do Serviço de Aplicações.][vnetintegration]

## <a name="app-service-environment"></a>Ambiente do Serviço de Aplicações 

Um Ambiente de Serviço de Aplicações (ASE) é uma única implantação de inquilino do Azure App Service que funciona no seu VNet. O ASE permite a utilização de casos como:

* Aceder a recursos no seu VNet
* Recursos de acesso em todo o ExpressRoute
* Exponha as suas aplicações com um endereço privado no seu VNet 
* Aceder a recursos através dos pontos finais de serviço 

Com um ASE, não precisa de utilizar funcionalidades como a Integração VNet ou pontos finais de serviço porque o ASE já está no seu VNet. Se pretender aceder a recursos como SQL ou Storage sobre os pontos finais de serviço, ative os pontos finais do serviço na sub-rede ASE. Se pretender aceder aos recursos no VNet, não é necessária nenhuma configuração adicional.  Se pretender aceder a recursos através do ExpressRoute, já se encontra no VNet e não precisa de configurar nada sobre o ASE ou as aplicações no seu interior. 

Como as aplicações num ILB ASE podem ser expostas num endereço IP privado, pode facilmente adicionar dispositivos WAF para expor apenas as aplicações que pretende para a internet e manter as restantes seguras. Presta-se a um fácil desenvolvimento de aplicações multi-camadas. 

Há algumas coisas que ainda não são possíveis do serviço multi-inquilino que são de um ASE. Estas incluem coisas como:

* Exponha as suas aplicações num endereço IP privado
* Proteja todo o tráfego de saída com controlos de rede que não fazem parte da sua app 
* Hospedar as suas aplicações num único serviço de inquilino 
* Escala até muitos mais casos do que são possíveis no serviço multi-inquilino 
* Carregue os certificados privados de clientes da AC para uso pelas suas apps com pontos finais seguros de CA privados 
* Force TLS 1.1 em todas as aplicações hospedadas no sistema sem qualquer capacidade de desativar ao nível da aplicação 
* Forneça um endereço de saída dedicado para todas as aplicações na sua ASE que não seja partilhada com nenhum cliente 

![ASE em um VNet](media/networking-features/app-service-environment.png)

O ASE fornece a melhor história em torno de hospedagem de apps isoladas e dedicadas, mas vem com alguns desafios de gestão. Algumas coisas a considerar antes de usar um ASE operacional são:
 
 * Um ASE corre dentro do seu VNet mas tem dependências fora do VNet. Essas dependências devem ser permitidas. Ler mais em [questões de Networking para um Ambiente de Serviço de Aplicações][networkinfo]
 * Um ASE não escala imediatamente como o serviço multi-inquilino. É preciso antecipar as necessidades de escala em vez de escalar reactivamente. 
 * Um ASE tem um custo adiantado mais elevado associado a ele. Para tirar o máximo partido do seu ASE, deve planear colocar muitas cargas de trabalho num ASE em vez de o ter usado para pequenos esforços.
 * As aplicações numa ASE não podem restringir o acesso a algumas aplicações numa ASE e não a outras.
 * A ASE encontra-se numa sub-rede e quaisquer regras de rede aplicam-se a todo o tráfego de e para aquela ASE. Se quiser atribuir regras de tráfego de entrada para apenas uma aplicação, utilize restrições de acesso. 

## <a name="combining-features"></a>Combinar características 

As características notadas para o serviço multi-inquilino podem ser usadas em conjunto para resolver casos de uso mais elaborados. Dois dos casos de utilização mais comuns são aqui descritos, mas são apenas exemplos. Ao entender o que as várias funcionalidades fazem, você pode resolver quase todas as necessidades de arquitetura do seu sistema.

### <a name="inject-app-into-a-vnet"></a>Injetar aplicativo num VNet

Um pedido comum é sobre como colocar a sua aplicação num VNet. Colocar a sua aplicação num VNet significa que os pontos finais de entrada e saída para uma aplicação estão dentro de um VNet. O ASE fornece a melhor solução para resolver este problema mas, você pode obter a maior parte do que é necessário com o serviço multi-inquilino, combinando características. Por exemplo, pode hospedar apenas aplicações intranet com endereços de entrada e saída privados:

* Criação de um Gateway de aplicações com endereço de entrada e saída privada
* Garantir o tráfego de entrada na sua app com pontos finais de serviço 
* Utilize a nova Integração VNet para que o backend da sua app esteja no seu VNet 

Este estilo de implementação não lhe daria um endereço dedicado para tráfego de saída para a internet ou lhe daria a capacidade de bloquear todo o tráfego de saída da sua app.  Este estilo de implementação dar-lhe-ia muito do que só conseguiria com um ASE. 

### <a name="create-multi-tier-applications"></a>Criar aplicações multi-camadas

Uma aplicação multi-nível é uma aplicação onde as aplicações de backend da API só podem ser acedidas a partir do nível frontal. Para criar uma aplicação multi-nível, pode:

* Utilize a Integração VNet para ligar o backend da sua aplicação web frontal com uma sub-rede num VNet
* Utilize pontos finais de serviço para garantir o tráfego de entrada na sua app API para apenas vir da sub-rede utilizada pela sua aplicação web frontal

![app multi-nível](media/networking-features/multi-tier-app.png)

Pode ter várias aplicações frontais que utilizam a mesma aplicação API utilizando a VNet Integration a partir de outras aplicações frontais e pontos finais de serviço da aplicação API com as suas sub-redes.  

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md