---
title: Funcionalidades de rede
description: Conheça as funcionalidades da rede no Serviço de Aplicações Azure e quais as funcionalidades que necessita para as necessidades da sua rede para segurança ou funcionalidade.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 02/27/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0fd904b15a830e2b261057a11d1a8f3a4d584fe1
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649231"
---
# <a name="app-service-networking-features"></a>Funcionalidades de rede de serviços de aplicação

As aplicações no Serviço de Aplicações Azure podem ser implementadas de várias formas. Por padrão, as aplicações hospedadas no App Service são diretamente acessíveis à Internet e só podem chegar a pontos finais hospedados na Internet. Muitas aplicações de clientes precisam, no entanto, de controlar o tráfego de rede de entrada e saída. Existem várias funcionalidades disponíveis no Serviço de Aplicações para satisfazer essas necessidades. O desafio é saber que funcionalidade deve ser usada para resolver um determinado problema. Este documento destina-se a ajudar os clientes a determinar que funcionalidade deve ser usada com base em alguns casos de utilização de exemplo.

Existem dois tipos de implantação primários para o Serviço de Aplicações Azure. Existe o serviço público multi-inquilino, que acolhe planos de Serviço de Aplicações nas SKUs de preços Gratuitos, Partilhados, Básicos, Standard, Premium e Premiumv2. Depois, há o único inquilino App Service Environment (ASE), que acolhe planos isolados do Serviço de Aplicações SKU diretamente na sua Rede Virtual Azure (VNet). As funcionalidades que utiliza variarão se estiver no serviço multi-inquilino ou numa ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Funcionalidades de rede de serviço sinuoso multi-inquilino 

O Serviço de Aplicações Azure é um sistema distribuído. As funções que tratam dos pedidos http/HTTPS são chamadas front-ends. Os papéis que acolhem a carga de trabalho do cliente são chamados trabalhadores. Todas as funções numa implementação do Serviço de Aplicações existem numa rede multi-arrendatária. Como existem muitos clientes diferentes na mesma unidade de escala de Serviço de Aplicações, não é possível ligar a rede de Serviços de Aplicações diretamente à sua rede. Em vez de ligar as redes, precisamos de funcionalidades para lidar com os diferentes aspetos da comunicação de aplicações. As funcionalidades que lidam com pedidos para a sua aplicação não podem ser usadas para resolver problemas ao fazer chamadas da sua aplicação. Da mesma forma, as funcionalidades que resolvem problemas de chamadas da sua aplicação não podem ser usadas para resolver problemas na sua aplicação.  

| Características de entrada | Características de saída |
|---------------------|-------------------|
| Endereço atribuído à aplicação | Ligações Híbridas |
| Restrições de acesso | Gateway exigia integração VNet |
| Pontos Finais de Serviço | Integração de VNet |

Salvo indicação em contrário, todas as funcionalidades podem ser usadas em conjunto. Pode misturar as funcionalidades para resolver os seus vários problemas.

## <a name="use-case-and-features"></a>Use o estojo e as características

Para qualquer caso de utilização, pode haver algumas formas de resolver o problema.  A característica certa a utilizar deve-se, por vezes, a razões para além do caso de utilização em si. Os seguintes casos de utilização de entrada sugerem como usar funcionalidades de rede do Serviço app para resolver problemas em torno do controlo do tráfego que vai para a sua aplicação. 
 
| Casos de utilização de entrada | Funcionalidade |
|---------------------|-------------------|
| Suporte as necessidades SSL baseadas em IP para a sua aplicação | Endereço atribuído à aplicação |
| Endereço de entrada não partilhado e dedicado para a sua aplicação | Endereço atribuído à aplicação |
| Restringir o acesso à sua aplicação a partir de um conjunto de endereços bem definidos | Restrições de acesso |
| Expor a minha aplicação em IPs privados no meu VNet | ILB ASE </br> Gateway de Aplicação com pontos finais de serviço |
| Restringir o acesso à minha app a partir de recursos num VNet | Pontos Finais de Serviço </br> ILB ASE |
| Exponha a minha aplicação num IP privado no meu VNet | ILB ASE </br> IP privado para entrada em um Gateway de aplicação com pontos finais de serviço |
| Proteja a minha aplicação com um WAF | Gateway de aplicação + ILB ASE </br> Gateway de Aplicação com pontos finais de serviço </br> Porta da frente azure com restrições de acesso |
| Carregar o tráfego de equilíbrio para as minhas apps em diferentes regiões | Porta da frente azure com restrições de acesso | 
| Tráfego de equilíbrio de carga na mesma região | [Gateway de aplicação com pontos finais de serviço][appgwserviceendpoints] | 

Os seguintes casos de utilização sugerem como utilizar funcionalidades de rede do Serviço de Aplicações para resolver as necessidades de acesso de saída para a sua aplicação. 

| Casos de utilização de saída | Funcionalidade |
|---------------------|-------------------|
| Acesso a recursos numa Rede Virtual Azure na mesma região | Integração de VNet </br> ASE |
| Acesso a recursos numa Rede Virtual Azure numa região diferente | Gateway exigia integração VNet </br> ASE e VNet espreitando |
| Recursos de acesso garantidos com pontos finais de serviço | Integração de VNet </br> ASE |
| Recursos de acesso numa rede privada não ligada ao Azure | Ligações Híbridas |
| Recursos de acesso através dos circuitos ExpressRoute | Integração de VNet </br> ASE | 
| Proteja o tráfego de saída da sua aplicação web | Grupos de Integração vNet e segurança da rede </br> ASE | 
| Rota de tráfego de saída da sua aplicação web | Tabelas de Integração e Rota VNet </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento de rede padrão

As unidades de escala de serviço de aplicação Azure suportam muitos clientes em cada implementação. Os planos SKU gratuitos e partilhados acolhem cargas de trabalho dos clientes em trabalhadores multi-inquilinos. Os planos Básicos, e acima dos planos, acolhem cargas de trabalho dos clientes que se dedicam a apenas um plano de Serviço de Aplicações (ASP). Se você tinha um plano de Serviço de Aplicações Padrão, então todas as aplicações desse plano serão executadas com o mesmo trabalhador. Se eliminar o trabalhador, todas as aplicações nessa ASP serão replicadas num novo trabalhador para cada instância no seu ASP. Os trabalhadores que são utilizados para o Premiumv2 são diferentes dos trabalhadores utilizados para os outros planos. Cada implementação do Serviço de Aplicações tem um endereço IP que é usado para todo o tráfego de entrada para as aplicações nessa implementação do Serviço de Aplicações. No entanto, existem entre 4 e 11 endereços usados para fazer chamadas de saída. Estes endereços são partilhados por todas as aplicações da implementação do Serviço de Aplicações. Os endereços de saída são diferentes com base nos diferentes tipos de trabalhadores. Isto significa que os endereços utilizados pelos ASPs Gratuitos, Partilhados, Básicos, Standard e Premium são diferentes dos endereços utilizados para chamadas de saída dos ASPs Premiumv2. Se procurar nas propriedades para a sua aplicação, pode ver os endereços de entrada e saída que são utilizados pela sua aplicação. Se precisar de bloquear uma dependência com um IP ACL, utilize os possíveis Endereços outbound. 

![Propriedades de aplicativos](media/networking-features/app-properties.png)

O Serviço de Aplicações tem uma série de pontos finais que são usados para gerir o serviço.  Estes endereços são publicados num documento separado e também estão na etiqueta de serviço IP appServiceManagement. A etiqueta AppServiceManagement só é utilizada com um App Service Environment (ASE) onde é necessário permitir esse tráfego. Os endereços de entrada do Serviço de Aplicações são rastreados na etiqueta de serviço IP appService. Não existe nenhuma etiqueta de serviço IP que contenha os endereços de saída utilizados pelo Serviço de Aplicações. 

![Diagrama de entrada e saída do Serviço de Aplicações](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Endereço atribuído à aplicação 

A funcionalidade de endereço atribuída à aplicação é um offshoot da capacidade SSL baseada em IP e é acedida através da configuração do SSL com a sua aplicação. Esta funcionalidade pode ser utilizada para chamadas SSL baseadas em IP, mas também pode ser usada para dar à sua aplicação um endereço que só tem. 

![Diagrama de endereço atribuído a aplicativo](media/networking-features/app-assigned-address.png)

Quando utiliza um endereço atribuído a uma aplicação, o seu tráfego continua a passar pelas mesmas funções front-front-end que tratam todo o tráfego de entrada na unidade de escala do Serviço de Aplicações. O endereço que é atribuído à sua aplicação, no entanto, só é utilizado pela sua aplicação. Os casos de utilização desta funcionalidade são:

* Suporte as necessidades SSL baseadas em IP para a sua aplicação
* Detete um endereço dedicado para a sua aplicação que não seja partilhado com mais nada

Pode aprender a definir um endereço na sua aplicação com o tutorial em [Configuração IP baseado em SSL][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrições de acesso 

A capacidade de Restrições de Acesso permite filtrar pedidos de **entrada** com base no endereço IP de origem. A ação de filtragem ocorre nas funções frontais que estão a montante das funções dos trabalhadores onde as suas aplicações estão em execução. Uma vez que as funções front-end são a montante dos trabalhadores, a capacidade de Restrições de Acesso pode ser considerada como proteção de nível de rede para as suas apps. A funcionalidade permite-lhe construir uma lista de blocos de endereços de permitir e negar que são avaliados em ordem prioritária. É semelhante à funcionalidade do Network Security Group (NSG) que existe em Rede Azure.  Pode utilizar esta funcionalidade numa ASE ou no serviço multi-inquilinos. Quando utilizado com um ILB ASE, pode restringir o acesso a partir de blocos de endereços privados.

![Restrições de acesso](media/networking-features/access-restrictions.png)

A funcionalidade Restrições de Acesso ajuda em cenários em que pretende restringir os endereços IP que podem ser utilizados para chegar à sua aplicação. Entre os casos de utilização desta funcionalidade encontram-se:

* Restringir o acesso à sua aplicação a partir de um conjunto de endereços bem definidos 
* Restrinja o acesso a um serviço de equilíbrio de carga, como a Porta da Frente Azure. Se quiser bloquear o seu tráfego de entrada para a Porta Da Frente Azure, crie regras para permitir o tráfego de 147.243.0.0/16 e 2a01:111:2050:/44. 

![Restrições de acesso com porta da frente](media/networking-features/access-restrictions-afd.png)

Se deseja bloquear o acesso à sua aplicação para que só possa ser alcançado a partir de recursos da sua Rede Virtual Azure (VNet), precisa de uma morada pública estática sobre qualquer que seja a sua fonte no seu VNet. Se os recursos não tiverem um endereço público, deverá utilizar a função De pontos finais de serviço. Saiba como ativar esta funcionalidade com o tutorial sobre restrições de [acesso de configuração][iprestrictions].

### <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos finais do serviço permitem bloquear o acesso **à entrada** da sua aplicação de modo a que o endereço de origem tenha de vir de um conjunto de subredes que seleciona. Esta funcionalidade funciona em conjunto com as Restrições de Acesso IP. Os pontos finais do serviço são definidos na mesma experiência do utilizador que as Restrições de Acesso IP. Pode construir uma lista de regras de acesso que inclua endereços públicos, bem como subredes nos seus VNets. Esta funcionalidade suporta cenários como:

![pontos finais de serviço](media/networking-features/service-endpoints.png)

* Configurar um Gateway de Aplicação com a sua app para bloquear o tráfego de entrada na sua app
* Restringindo o acesso à sua app aos recursos no seu VNet. Isto pode incluir VMs, ASEs ou mesmo outras aplicações que usam integração VNet 

![pontos finais de serviço com gateway de aplicação](media/networking-features/service-endpoints-appgw.png)

Pode saber mais sobre configurar pontos finais de serviço com a sua aplicação no tutorial sobre restrições de acesso ao [fim do serviço de configuração][serviceendpoints]
 
### <a name="hybrid-connections"></a>Ligações Híbridas

O Serviço de Aplicações Ligações Híbridas permite que as suas apps efaçam chamadas **de saída** para pontos finais específicos do TCP. O ponto final pode estar no local, num VNet ou em qualquer lugar que permita o tráfego de saída para Azure no porto 443. A funcionalidade requer a instalação de um agente de retransmissão chamado Hybrid Connection Manager (HCM) num Windows Server 2012 ou num novo anfitrião. O HCM precisa de ser capaz de chegar ao Relé Azure na porta 443. O HCM pode ser descarregado a partir do App Service Hybrid Connections UI no portal. 

![Fluxo de rede de conexões híbridas](media/networking-features/hybrid-connections.png)

A funcionalidade De Ligações Híbridas do Serviço app é construída com base na capacidade de Ligações Híbridas Azure Relay. O Serviço de Aplicações utiliza uma forma especializada da funcionalidade que apenas suporta fazer chamadas de saída da sua app para um anfitrião e porta TCP. Este hospedeiro e porta só precisam de ser resolvidos no hospedeiro onde o HCM está instalado. Quando a aplicação, no Serviço de Aplicações, faz uma verificação DNS sobre o hospedeiro e a porta definida sem ligação híbrida, o tráfego é automaticamente redirecionado para passar pela Ligação Híbrida e sair do Hybrid Connection Manager. Para saber mais sobre Ligações Híbridas, leia a documentação sobre [ligações híbridas do Serviço de Aplicações][hybridconn]

Esta funcionalidade é comumente usada para:

* Aceder a recursos em redes privadas que não estejam ligados ao Azure com uma VPN ou ExpressRoute
* Suporte levantar e transferir aplicações no local para O Serviço de Aplicações sem necessidade de mover também bases de dados de suporte  
* Forneça de forma segura acesso a um único hospedeiro e porta por conexão híbrida. A maioria das funcionalidades de networking abre acesso a uma rede e com Conexões Híbridas só tem o único anfitrião e porta que pode alcançar.
* Cobrir cenários não abrangidos por outros métodos de conectividade de saída
* Realizar desenvolvimento no App Service onde as aplicações podem facilmente alavancar recursos no local 

Uma vez que a funcionalidade permite o acesso aos recursos no local sem um buraco de firewall de entrada, é popular entre os desenvolvedores. As outras funcionalidades de rede de serviços de aplicações de saída estão muito relacionadas com a Rede Virtual Azure. As Ligações Híbridas não têm uma dependência de passar por um VNet e podem ser usadas para uma maior variedade de necessidades de networking. É importante notar que a funcionalidade De Ligações Híbridas do Serviço app não se importa ou sabe o que está a fazer em cima dela. Isto é, pode usá-lo para aceder a uma base de dados, a um serviço web ou a uma tomada tCP arbitrária num computador principal. A funcionalidade essencialmente túneis pacotes TCP. 

Embora a Hybrid Connections seja popular para o desenvolvimento, também é usada em numerosas aplicações de produção. É ótimo para aceder a um serviço web ou base de dados, mas não é apropriado para situações que envolvam a criação de muitas conexões. 

### <a name="gateway-required-vnet-integration"></a>Gateway exigia integração VNet 

A funcionalidade de integração VNet do Serviço de Aplicação necessária permite que a sua aplicação evoque pedidos de **saída** numa Rede Virtual Azure. A funcionalidade funciona ligando o anfitrião que a sua aplicação está a correr para um portal de rede virtual no seu VNet com uma VPN ponto-a-site. Ao configurar a funcionalidade, a sua aplicação obtém um dos endereços ponto-a-site atribuídos a cada instância. Esta funcionalidade permite-lhe aceder a recursos em VNets Clássicos ou Gestorde Recursos em qualquer região. 

![Gateway exigia integração VNet](media/networking-features/gw-vnet-integration.png)

Esta funcionalidade resolve o problema de aceder a recursos noutros VNets e pode mesmo ser usada para ligar através de um VNet a outros VNets ou até mesmo no local. Não funciona com VNets ligados à ExpressRoute, mas sim com redes conectadas VPN site-to-site. Normalmente é inapropriado utilizar esta funcionalidade a partir de uma aplicação num App Service Environment (ASE), porque a ASE já se encontra no seu VNet. Os casos de utilização que esta funcionalidade resolve são:

* Acesso a recursos em IPs privados nas suas redes virtuais Azure 
* Acesso a recursos no local se houver uma VPN site-to-site 
* Acesso a recursos em VNets peered 

Quando esta funcionalidade estiver ativada, a sua aplicação utilizará o servidor DNS com o qual o destino VNet está configurado. Pode ler mais sobre esta funcionalidade na documentação sobre a [Integração VNet do Serviço de Aplicações.][vnetintegrationp2s] 

### <a name="vnet-integration"></a>Integração de VNet

A funcionalidade de Integração VNet necessária é muito útil, mas ainda não resolve o acesso aos recursos através do ExpressRoute. Além de precisar de contactar as ligações ExpressRoute, é necessário que as aplicações possam fazer chamadas para serviços seguros de ponto final. Para resolver ambas as necessidades adicionais, foi adicionada outra capacidade de Integração VNet. A nova funcionalidade de Integração VNet permite-lhe colocar o backend da sua aplicação numa subnet numa VNet gestora de recursos na mesma região. Esta funcionalidade não está disponível a partir de um App Service Environment, que já se encontra num VNet. Esta funcionalidade permite:

* Acesso a recursos em VNets gestor de recursos na mesma região
* Acesso a recursos que são assegurados com pontos finais de serviço 
* Acesso a recursos acessíveis através de conexões ExpressRoute ou VPN
* Assegurar todo o tráfego de saída 
* Forçar a escavar todo o tráfego de saída. 

![Integração de VNet](media/networking-features/vnet-integration.png)

Para saber mais sobre esta funcionalidade, leia os docs no [App Service VNet Integration][vnetintegration].

## <a name="app-service-environment"></a>Ambiente do Serviço de Aplicações 

Um App Service Environment (ASE) é uma implantação única do Serviço de Aplicações Azure que funciona no seu VNet. A ASE permite a utilização de casos como:

* Acesso a recursos no seu VNet
* Acesso a recursos através do ExpressRoute
* Exponha as suas apps com um endereço privado no seu VNet 
* Aceder a recursos através de pontos finais de serviço 

Com uma ASE, não precisa de utilizar funcionalidades como a Integração VNet ou pontos finais de serviço porque a ASE já se encontra no seu VNet. Se pretender aceder a recursos como o SQL ou o Storage sobre os pontos finais do serviço, ative pontos finais de serviço na subnet ASE. Se quiser aceder aos recursos no VNet, não é necessária uma configuração adicional.  Se quiser aceder a recursos através do ExpressRoute, já se encontra no VNet e não precisa de configurar nada na ASE ou nas aplicações no seu interior. 

Como as aplicações de um ILB ASE podem ser expostas num endereço IP privado, pode facilmente adicionar dispositivos WAF para expor apenas as aplicações que deseja para a internet e manter o resto seguro. Presta-se a um fácil desenvolvimento de aplicações de vários níveis. 

Há algumas coisas que ainda não são possíveis do serviço multi-inquilinos que são de uma ASE. Estas incluem coisas como:

* Exponha as suas aplicações num endereço IP privado
* Proteja todo o tráfego de saída com controlos de rede que não fazem parte da sua app 
* Hospedar as suas apps num único serviço de inquilinos 
* Escala até muitos mais instâncias do que é possível no serviço multi-inquilinos 
* Carregue certificados de cliente ca privados para uso pelas suas apps com pontos finais seguros CA privados 
* Force TLS 1.1 em todas as aplicações hospedadas no sistema sem qualquer capacidade de desativar ao nível da aplicação 
* Forneça um endereço de saída dedicado para todas as aplicações da sua ASE que não seja partilhada com nenhum cliente 

![ASE em uma VNet](media/networking-features/app-service-environment.png)

A ASE oferece a melhor história em torno de hospedagem de apps isoladas e dedicadas, mas vem com alguns desafios de gestão. Algumas coisas a considerar antes de usar uma ASE operacional são:
 
 * Uma ASE corre dentro do seu VNet mas tem dependências fora do VNet. Essas dependências devem ser permitidas. Ler mais em considerações de [Networking para um Ambiente de Serviço de Aplicações][networkinfo]
 * Uma ASE não escala imediatamente como o serviço multi-inquilinos. É preciso antecipar as necessidades de escala em vez de escalar reactivamente. 
 * Uma ASE tem um custo frontal mais elevado associado a ele. Para tirar o máximo partido da sua ASE, deve planear colocar muitas cargas de trabalho numa ASE em vez de a utilizar para pequenos esforços.
 * As aplicações numa ASE não podem restringir o acesso a algumas aplicações numa ASE e não noutras.
 * A ASE encontra-se numa subrede e todas as regras de rede aplicam-se a todo o tráfego de e para a ASE. Se quiser atribuir regras de tráfego de entrada para apenas uma aplicação, utilize restrições de acesso. 

## <a name="combining-features"></a>Combinando funcionalidades 

As funcionalidades notadas para o serviço multi-inquilinos podem ser utilizadas em conjunto para resolver casos de utilização mais elaborados. Dois dos casos de utilização mais comuns são descritos aqui, mas são apenas exemplos. Ao entender o que as várias funcionalidades fazem, pode resolver quase todas as necessidades da arquitetura do seu sistema.

### <a name="inject-app-into-a-vnet"></a>Injetar app num VNet

Um pedido comum é sobre como colocar a sua aplicação num VNet. Colocar a sua aplicação num VNet significa que os pontos finais de entrada e saída para uma aplicação estão dentro de um VNet. A ASE fornece a melhor solução para resolver este problema, mas pode obter a maior parte do que é necessário no serviço multi-inquilinos, combinando funcionalidades. Por exemplo, pode alojar aplicações intranet apenas com endereços de entrada e saída privados por:

* Criação de um Gateway de Aplicação com endereço de entrada e saída privado
* Garantir o tráfego de entrada na sua app com pontos finais de serviço 
* Use a nova Integração VNet para que o backend da sua aplicação esteja no seu VNet 

Este estilo de implementação não lhe daria um endereço dedicado para tráfego de saída para a internet ou lhe daria a capacidade de bloquear todo o tráfego de saída da sua app.  Este estilo de implantação dar-lhe-ia muito do que só de outra forma conseguiria com uma ASE. 

### <a name="create-multi-tier-applications"></a>Criar aplicações de vários níveis

Uma aplicação de vários níveis é uma aplicação onde as aplicações de backend da API só podem ser acedidas a partir do nível frontal. Para criar uma aplicação de vários níveis, pode:

* Utilize a Integração VNet para ligar o backend da sua aplicação web frontal com uma subnet num VNet
* Utilize pontos finais de serviço para garantir o tráfego de entrada na sua aplicação API apenas para vir da subnet utilizada pela sua aplicação web frontal

![app multi-tier](media/networking-features/multi-tier-app.png)

Pode ter várias aplicações front-end a utilizar a mesma aplicação API utilizando a Integração VNet a partir das outras aplicações frontais e pontos finais de serviço da aplicação API com as suas subredes.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
