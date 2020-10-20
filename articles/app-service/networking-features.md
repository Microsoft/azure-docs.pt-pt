---
title: Funcionalidades de rede
description: Conheça as funcionalidades da rede no Azure App Service e quais as funcionalidades que necessita para as necessidades da sua rede para segurança ou funcionalidade.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 860b1ac1713ac7afb7db2643d68974b399b5236b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207065"
---
# <a name="app-service-networking-features"></a>Funcionalidades de networking do Serviço de Aplicações

As aplicações no Azure App Service podem ser implementadas de várias maneiras. Por padrão, as aplicações hospedadas no Serviço de Aplicações são diretamente acessíveis à Internet e só podem chegar aos pontos finais hospedados na Internet. Muitas aplicações de clientes precisam, no entanto, de controlar o tráfego de rede de entrada e saída. Existem várias funcionalidades disponíveis no Serviço de Aplicações para satisfazer essas necessidades. O desafio é saber que característica deve ser usada para resolver um determinado problema. Este documento destina-se a ajudar os clientes a determinar que funcionalidade deve ser utilizada com base em alguns casos de utilização de exemplo.

Existem dois tipos de implementação primária para o Serviço de Aplicações Azure. Existe o serviço público multi-inquilino, que acolhe planos de Serviço de Aplicações nos SKUs de preços gratuitos, partilhados, básicos, padrão, premium, premiumv2 e premiumv3. Depois há o único inquilino App Service Environment (ASE), que acolhe planos isolados do SKU App Service diretamente na sua Rede Virtual Azure (VNet). As funcionalidades que utiliza variarão se estiver no serviço multi-inquilino ou num ASE. 

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
| Restringir o acesso à minha app a partir de recursos numa VNet | Pontos Finais de Serviço </br> ILB ASE </br> Pontos finais privados |
| Expor a minha aplicação num IP privado no meu VNet | ILB ASE </br> Pontos finais privados </br> IP privado para entrada em um Gateway de aplicação com pontos finais de serviço |
| Proteja a minha aplicação com uma Firewall de Aplicação Web (WAF) | Gateway de aplicação + ILB ASE </br> Gateway de aplicação com pontos finais privados </br> Gateway de aplicação com pontos finais de serviço </br> Porta frontal Azure com restrições de acesso |
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

As unidades de escala de serviço de aplicações Azure suportam muitos clientes em cada implementação. Os planos SKU gratuitos e partilhados acolhem cargas de trabalho dos clientes em trabalhadores multi-inquilinos. O Basic, e acima de planos, acolhe cargas de trabalho de clientes que são dedicadas a apenas um plano de Serviço de Aplicações (ASP). Se tiver um plano standard de Serviço de Aplicações, todas as aplicações nesse plano serão executadas com o mesmo trabalhador. Se escaloná-lo, todas as aplicações dessa ASP serão replicadas num novo trabalhador para cada instância no seu ASP. 

#### <a name="outbound-addresses"></a>Endereços de saída

Os VM dos trabalhadores são discriminadas em grande parte pelos planos de preços do Serviço de Aplicações. Os Free, Shared, Basic, Standard e Premium utilizam todos o mesmo tipo de VM do trabalhador. Premiumv2 está em outro tipo VM. Premiumv3 está em mais um tipo VM. A cada mudança na família VM, há um conjunto diferente de endereços de saída. Se escalar de Standard para Premiumv2, os seus endereços de saída mudarão. Se escalar de Premiumv2 para Premiumv3, os seus endereços de saída mudarão. Existem algumas unidades de escala mais antigas que vão alterar os endereços de entrada e saída quando escala de Standard para Premiumv2. Há uma série de endereços usados para fazer chamadas de saída. Os endereços de saída utilizados pela sua aplicação para fazer chamadas de saída estão listados nas Propriedades para a sua aplicação. Estes endereços são partilhados por todas as aplicações em execução na mesma família de VM trabalhadores nessa implementação do Serviço de Aplicações. Se quiser ver todos os endereços possíveis que a sua aplicação poderá utilizar nessa unidade de escala, existe outra propriedade chamada PossívelOutboundAddresses que os irá listar. 

![Propriedades de aplicativos](media/networking-features/app-properties.png)

O Serviço de Aplicações tem uma série de pontos finais que são usados para gerir o serviço.  Estes endereços são publicados num documento separado e também estão na etiqueta de serviço IP appServiceManagement. A etiqueta AppServiceManagement é utilizada apenas com um Ambiente de Serviço de Aplicações onde é necessário permitir esse tráfego. Os endereços de entrada do Serviço de Aplicações são rastreados na etiqueta de serviço IP do AppService. Não existe uma etiqueta de serviço IP que contenha os endereços de saída utilizados pelo Serviço de Aplicações. 

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

Os pontos finais de serviço permitem-lhe bloquear o **acesso** à sua aplicação de modo a que o endereço de origem tenha vindo de um conjunto de sub-redes que seleciona. Esta funcionalidade funciona em conjunto com as Restrições de Acesso IP. Os pontos finais de serviço não são compatíveis com a depuração remota. Para utilizar a depuração remota com a sua aplicação, o seu cliente não pode estar numa sub-rede com pontos finais de Serviço ativados. Os pontos finais de serviço são definidos na mesma experiência do utilizador que as Restrições de Acesso IP. Pode construir uma lista de regras de acesso que incluem endereços públicos, bem como sub-redes nos seus VNets. Esta funcionalidade suporta cenários como:

![pontos finais de serviço](media/networking-features/service-endpoints.png)

* Configurar um Gateway de Aplicações com a sua app para bloquear o tráfego de entrada na sua app
* Restringindo o acesso à sua app a recursos no seu VNet. Isto pode incluir VMs, ASEs ou até mesmo outras aplicações que usam a Integração VNet 

![pontos finais de serviço com gateway de aplicação](media/networking-features/service-endpoints-appgw.png)

Pode saber mais sobre configurar pontos finais de serviço com a sua aplicação no tutorial sobre [restrições de acesso ao ponto final do serviço de configuração][serviceendpoints]

### <a name="private-endpoints"></a>Pontos Finais Privados

Private Endpoint é uma interface de rede que o liga de forma privada e segura à sua Web App por Azure Private Link. O Private Endpoint utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo a Web App para o seu VNet. Esta funcionalidade **destina-se** apenas aos fluxos de entrada para a sua Web App.
[Utilização de pontos finais privados para app Web Azure][privateendpoints]

Os pontos finais privados permitem cenários como:

* Restringir o acesso à minha app a partir de recursos numa VNet 
* Expor a minha aplicação num IP privado no meu VNet 
* Proteja a minha app com um WAF 

Os pontos finais privados impedem a exfiltração de dados, pois a única coisa que pode alcançar através do ponto final privado é a aplicação com a qual está configurada. 
 
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

Como a funcionalidade permite o acesso a recursos no local sem um buraco de firewall de entrada, é popular entre os desenvolvedores. As outras funcionalidades de rede de serviço de aplicações de saída estão relacionadas com a rede virtual Azure. As Ligações Híbridas não têm dependência de passar por um VNet e podem ser usadas para uma maior variedade de necessidades de networking. É importante notar que a funcionalidade De Ligações Híbridas do Serviço de Aplicações não se importa nem sabe o que está a fazer em cima dela. Ou seja, pode usá-lo para aceder a uma base de dados, a um serviço web ou a uma tomada TCP arbitrária num computador central. A característica essencialmente túneis pacotes TCP. 

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

A funcionalidade de Integração VNet exigida pelo gateway é útil, mas ainda não resolve o acesso aos recursos através do ExpressRoute. Além da necessidade de chegar a todas as ligações ExpressRoute, existe a necessidade de as aplicações poderem fazer chamadas para serviços de serviço seguros. Para resolver ambas as necessidades adicionais, foi adicionada outra capacidade de Integração VNet. A nova funcionalidade de Integração VNet permite-lhe colocar o backend da sua aplicação numa sub-rede numa VNet do Gestor de Recursos na mesma região. Esta funcionalidade não está disponível num Ambiente de Serviço de Aplicações, que já se encontra num VNet. Esta funcionalidade permite:

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

Uma aplicação multi-nível é uma aplicação onde as aplicações de backend da API só podem ser acedidas a partir do nível frontal. Há duas formas de criar uma aplicação multi-nível. Ambos começam por utilizar a Integração VNet para ligar a sua aplicação web frontal a uma sub-rede num VNet. Isto permitirá que a sua aplicação web faça chamadas para o seu VNet. Depois da sua aplicação frontal estar ligada ao VNet, tem de escolher como bloquear o acesso à sua aplicação API.  Pode:

* hospedar a aplicação front-end e API no mesmo ILB ASE e expor a aplicação frontal à internet com um gateway de aplicações
* hospedar a frente no serviço multi-inquilino e o backend em um ILB ASE
* hospedar a aplicação front-end e API no serviço multi-inquilino

Se estiver a hospedar a aplicação front-end e API para uma aplicação multi-nível, pode:

Exponha a sua aplicação API com pontos finais privados no seu VNet

![pontos finais privados de dois níveis app](media/networking-features/multi-tier-app-private-endpoint.png)

Utilize pontos finais de serviço para garantir o tráfego de entrada na sua app API para apenas vir da sub-rede utilizada pela sua aplicação web frontal

![aplicativo de endpoints de serviço garantido](media/networking-features/multi-tier-app.png)

As trocas entre as duas técnicas são:

* com os pontos finais de serviço, só tem de garantir o tráfego da sua app API à sub-rede de integração. Isto protege a aplicação API, mas ainda pode ter uma possibilidade de exfiltração de dados da sua aplicação frontal para outras aplicações no Serviço de Aplicações.
* com pontos finais privados tem duas sub-redes em jogo. Isto aumenta a complexidade. Além disso, o ponto final privado é um recurso de alto nível e adiciona mais para gerir. O benefício da utilização de pontos finais privados é que não tem uma possibilidade de exfiltração de dados. 

Qualquer uma das técnicas funcionará com várias extremidades frontais. Em pequena escala, os pontos finais de serviço são muito mais fáceis de usar porque simplesmente ativa os pontos finais de serviço para a aplicação API na sub-rede de integração frontal. À medida que adiciona mais aplicações frontais, tem de ajustar todas as aplicações da API para ter pontos finais de serviço com a sub-rede de integração. Com os pontos finais privados, você tem mais complexidade, mas você não precisa mudar nada nas suas aplicações API depois de definir um ponto final privado. 

### <a name="line-of-business-applications"></a>Aplicações de linha de negócio

As aplicações de linha de negócios (LOB) são aplicações internas que normalmente não estão expostas para acesso a partir da internet. Estas aplicações são chamadas de dentro das redes corporativas onde o acesso pode ser estritamente controlado. Se utilizar um ILB ASE, é fácil hospedar as suas aplicações de linha de negócio. Se utilizar o serviço multi-inquilino, pode utilizar pontos finais privados ou pontos finais de serviço combinados com um Gateway de Aplicação. Existem duas razões para utilizar um Gateway de aplicação com pontos finais de serviço em vez de pontos finais privados:

* você precisa de proteção WAF nas suas aplicações LOB
* você quer carregar o equilíbrio para várias instâncias das suas aplicações LOB

Se nenhum dos dois for, é melhor usar pontos finais privados. Com os pontos finais privados disponíveis no Serviço de Aplicações, pode expor as suas aplicações em endereços privados no seu VNet. O ponto final privado que coloca no seu VNet pode ser alcançado através das ligações ExpressRoute e VPN. Configurar os pontos finais privados irá expor as suas aplicações num endereço privado, mas terá de configurar o DNS para chegar a esse endereço a partir do local. Para que isto funcione, terá de encaminhar a zona privada Azure DNS contendo os seus pontos finais privados para os servidores DNS no local. As zonas privadas Azure DNS não suportam o encaminhamento de zonas, mas pode suportar isso usando um servidor DNS para o efeito. Este modelo, [DNS Forwarder,](https://azure.microsoft.com/resources/templates/301-dns-forwarder/)facilita o encaminhamento da sua zona privada Azure DNS para os servidores DNS no local.

## <a name="app-service-ports"></a>Portas de Serviço de Aplicações

Se pesquisar o Serviço de Aplicações, encontrará várias portas expostas para ligações de entrada. Não há como bloquear ou controlar o acesso a estes portos no serviço multi-inquilinos. As portas expostas são as seguintes:

| Utilização | Portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Gestão | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuragem remota do Estúdio Visual  |  4020, 4022, 4024 |
|  Serviço de implementação web | 8172 |
|  Utilização de infraestruturas | 7654, 1221 |

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
