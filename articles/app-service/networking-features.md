---
title: Funcionalidades de rede
description: Conheça as funcionalidades de networking no Azure App Service e saiba quais as funcionalidades que necessita para segurança ou outra funcionalidade.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 39a511601606118228ee5fbd9dcf68b6707ede47
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288356"
---
# <a name="app-service-networking-features"></a>Funcionalidades de networking do Serviço de Aplicações

Pode implementar aplicações no Azure App Service de várias maneiras. Por padrão, as aplicações hospedadas no Serviço de Aplicações são acessíveis diretamente através da internet e podem chegar apenas a pontos finais hospedados na Internet. Mas para muitas aplicações, é necessário controlar o tráfego de rede de entrada e saída. Existem várias funcionalidades no Serviço de Aplicações para ajudá-lo a satisfazer essas necessidades. O desafio é saber qual a característica a usar para resolver um determinado problema. Este artigo irá ajudá-lo a determinar qual a função a utilizar, com base em alguns casos de uso de exemplo.

Existem dois tipos principais de implementação para o Azure App Service: 
- O serviço público multitenant acolhe planos de Serviço de Aplicações nos SKUs de preços gratuitos, partilhados, básicos, padrão, premium, PremiumV2 e PremiumV3. 
- O Single-tenant App Service Environment (ASE) acolhe planos isolados do Serviço de Aplicações SKU diretamente na sua rede virtual Azure. 

As funcionalidades que utiliza dependerão se estiver no serviço multitenant ou num ASE. 

## <a name="multitenant-app-service-networking-features"></a>Funcionalidades de networking multitenant App Service 

O Azure App Service é um sistema distribuído. As funções que lidam com pedidos HTTP ou HTTPS são chamadas *de extremidades frontais*. Os papéis que acolhem a carga de trabalho do cliente são *chamados de trabalhadores.* Todos os papéis numa implementação do Serviço de Aplicações existem numa rede multitenante. Como existem muitos clientes diferentes na mesma unidade de escala de Serviço de Aplicações, não é possível ligar a rede de Serviço de Aplicações diretamente à sua rede. 

Em vez de ligar as redes, precisa de funcionalidades para lidar com os vários aspetos da comunicação de aplicações. As funcionalidades que lidam com pedidos *à* sua aplicação não podem ser usadas para resolver problemas quando está a fazer chamadas *a partir da* sua aplicação. Da mesma forma, as funcionalidades que resolvem problemas com chamadas da sua app não podem ser usadas para resolver problemas na sua aplicação.  

| Características de entrada | Características de saída |
|---------------------|-------------------|
| Endereço atribuído a aplicativos | Ligações Híbridas |
| Restrições de acesso | Integração VNet exigida pelo Gateway |
| Pontos finais de serviço | Integração de VNet |
| Pontos finais privados ||

Além de exceções notadas, pode utilizar todas estas funcionalidades em conjunto. Pode misturar as funcionalidades para resolver os seus problemas.

## <a name="use-cases-and-features"></a>Use casos e funcionalidades

Para qualquer caso de uso, pode haver algumas maneiras de resolver o problema. Escolher a melhor característica às vezes vai além do próprio caso de uso. Os seguintes casos de utilização de entrada sugerem como usar funcionalidades de rede do Serviço de Aplicações para resolver problemas com o controlo do tráfego que vai para a sua aplicação:
 
| Caso de utilização de entrada | Funcionalidade |
|---------------------|-------------------|
| Suporte necessidades SSL baseadas em IP para a sua aplicação | Endereço atribuído a aplicativos |
| Suporte endereço de entrada dedicado não partilhado para a sua aplicação | Endereço atribuído a aplicativos |
| Restringir o acesso à sua aplicação a partir de um conjunto de endereços bem definidos | Restrições de acesso |
| Restringir o acesso à sua app a partir de recursos numa rede virtual | Pontos finais de serviço </br> ILB ASE </br> Pontos finais privados |
| Exponha a sua aplicação num IP privado na sua rede virtual | ILB ASE </br> Pontos finais privados </br> IP privado para tráfego de entrada em uma instância de Gateway de aplicação com pontos finais de serviço |
| Proteja a sua aplicação com uma firewall de aplicação web (WAF) | Gateway de aplicações e ILB ASE </br> Gateway de aplicação com pontos finais privados </br> Gateway de Aplicação com pontos finais de serviço </br> Porta frontal Azure com restrições de acesso |
| Carregar o tráfego de equilíbrio para as suas apps em diferentes regiões | Porta frontal Azure com restrições de acesso | 
| Tráfego de balanço de carga na mesma região | [Gateway de Aplicação com pontos finais de serviço][appgwserviceendpoints] | 

Os seguintes casos de utilização de saída sugerem como usar as funcionalidades de rede do Serviço de Aplicações para resolver as necessidades de acesso de saída para a sua aplicação:

| Caso de utilização de saída | Funcionalidade |
|---------------------|-------------------|
| Recursos de acesso numa rede virtual Azure na mesma região | Integração de VNet </br> ASE |
| Aceder a recursos numa rede virtual Azure numa região diferente | Integração VNet exigida pelo Gateway </br> Ase e rede virtual de observação |
| Recursos de acesso protegidos com pontos finais de serviço | Integração de VNet </br> ASE |
| Recursos de acesso numa rede privada que não está ligada ao Azure | Ligações Híbridas |
| Recursos de acesso em todos os circuitos Azure ExpressRoute | Integração de VNet </br> ASE | 
| Tráfego de saída seguro a partir da sua aplicação web | Integração VNet e grupos de segurança de rede </br> ASE | 
| Encaminhe o tráfego de saída a partir da sua aplicação web | Integração VNet e tabelas de rotas </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento de rede padrão

As unidades de escala de serviço de aplicações Azure suportam muitos clientes em cada implementação. Os planos SKU gratuitos e partilhados acolhem cargas de trabalho dos clientes em trabalhadores multitenantes. Os planos básicos e mais altos acolhem cargas de trabalho dos clientes que se dedicam apenas a um plano de Serviço de Aplicações. Se tiver um plano standard de Serviço de Aplicações, todas as aplicações nesse plano serão executadas com o mesmo trabalhador. Se escaloná-lo, todas as aplicações desse plano de Serviço de Aplicações serão replicadas num novo trabalhador para cada instância no seu plano de Serviço de Aplicações. 

#### <a name="outbound-addresses"></a>Endereços de saída

Os VM do trabalhador são discriminadas em grande parte pelos planos do Serviço de Aplicações. Os planos Free, Shared, Basic, Standard e Premium utilizam todos o mesmo tipo de VM do trabalhador. O plano PremiumV2 utiliza outro tipo VM. PremiumV3 usa mais um tipo VM. Quando mudas a família VM, recebes um conjunto diferente de endereços de saída. Se escalar de Standard para PremiumV2, os seus endereços de saída mudarão. Se escalar de PremiumV2 para PremiumV3, os seus endereços de saída mudarão. Em algumas unidades de escala mais antiga, tanto os endereços de entrada como de saída mudarão quando escalar de Standard para PremiumV2. 

Há uma série de endereços que são usados para chamadas de saída. Os endereços de saída utilizados pela sua aplicação para fazer chamadas de saída estão listados nas propriedades da sua aplicação. Estes endereços são partilhados por todas as aplicações em execução na mesma família de VM trabalhador na implementação do Serviço de Aplicações. Se quiser ver todos os endereços que a sua aplicação pode usar numa unidade de escala, há uma propriedade chamada `possibleOutboundAddresses` que os listará. 

![Screenshot que mostra propriedades de aplicativos.](media/networking-features/app-properties.png)

O Serviço de Aplicações tem uma série de pontos finais que são usados para gerir o serviço.  Esses endereços são publicados num documento separado e também estão na etiqueta de `AppServiceManagement` serviço IP. A `AppServiceManagement` etiqueta é utilizada apenas em Ambientes de Serviço de Aplicações onde é necessário permitir esse tráfego. Os endereços de entrada do Serviço de Aplicações são rastreados na `AppService` etiqueta de serviço IP. Não existe uma etiqueta de serviço IP que contenha os endereços de saída utilizados pelo Serviço de Aplicações. 

![Diagrama que mostra o tráfego de entrada e saída do Serviço de Aplicações.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Endereço atribuído a aplicativos 

A funcionalidade de endereço atribuída à aplicação é um offshoot da capacidade SSL baseada em IP. Acede-lo configurando sSL com a sua aplicação. Pode utilizar esta funcionalidade para chamadas SSL baseadas em IP. Também pode usá-lo para dar à sua app um endereço que só ele tem. 

![Diagrama que ilustra o endereço atribuído a aplicações.](media/networking-features/app-assigned-address.png)

Quando utiliza um endereço atribuído a uma aplicação, o seu tráfego ainda passa pelas mesmas funções frontais que manuseiam todo o tráfego de entrada na unidade de escala de Serviço de Aplicações. Mas o endereço atribuído à sua aplicação é utilizado apenas pela sua aplicação. Utilize casos para esta funcionalidade:

* Suporte necessidades SSL baseadas em IP para a sua aplicação.
* Desaprova um endereço dedicado para a sua aplicação que não seja partilhado.

Para saber como definir um endereço na sua aplicação, consulte [adicionar um certificado TLS/SSL no Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrições de acesso 

As restrições de acesso permitem filtrar pedidos *de entrada.* A ação de filtragem ocorre nas funções frontais que estão a montante das funções de trabalhador onde as suas aplicações estão a decorrer. Como as funções frontais são a montante dos trabalhadores, pode pensar nas restrições de acesso como proteção ao nível da rede para as suas apps. 

Esta funcionalidade permite-lhe construir uma lista de regras de permitir e negar que são avaliadas por ordem prioritária. É semelhante à funcionalidade do grupo de segurança da rede (NSG) na rede Azure. Pode utilizar esta função num ASE ou no serviço multitenant. Quando o utiliza com um ase ILB ou um ponto final privado, pode restringir o acesso a partir de blocos de endereços privados.
> [!NOTE]
> Até 512 regras de restrição de acesso podem ser configuradas por aplicação. 

![Diagrama que ilustra as restrições de acesso.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>Regras de restrição de acesso baseadas em IP

A funcionalidade de restrições de acesso baseadas em IP ajuda quando pretende restringir os endereços IP que podem ser usados para chegar à sua aplicação. São suportados IPv4 e IPv6. Alguns usam casos para esta funcionalidade:
* Restringir o acesso à sua aplicação a partir de um conjunto de endereços bem definidos. 
* Restringir o acesso ao tráfego através de um serviço de equilíbrio de carga, como a Porta frontal Azure. Se quiser bloquear o tráfego de entrada até a Porta frontal Azure, crie regras para permitir o tráfego a partir das 147.243.0.0/16 e 2a01:111:2050::/44. 

Para saber como ativar esta funcionalidade, consulte [configurar restrições de acesso][iprestrictions].

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Regras de restrição de acesso baseadas em pontos finais de serviço 

Os pontos finais de serviço permitem-lhe bloquear o *acesso* à sua aplicação para que o endereço de origem tenha vindo de um conjunto de sub-redes que seleciona. Esta funcionalidade funciona em conjunto com as restrições de acesso IP. Os pontos finais de serviço não são compatíveis com a depuração remota. Se pretender utilizar a depuração remota com a sua aplicação, o seu cliente não pode estar numa sub-rede que tenha pontos finais de serviço ativados. O processo de definição de pontos finais de serviço é semelhante ao processo de definição de restrições de acesso IP. Pode construir uma lista de regras de acesso que incluem endereços públicos e sub-redes nas suas redes virtuais. 

Alguns usam casos para esta funcionalidade:

* Crie um gateway de aplicações com a sua app para bloquear o tráfego de entrada na sua aplicação.
* Restringir o acesso à sua app a recursos na sua rede virtual. Estes recursos podem incluir VMs, ASEs ou até mesmo outras aplicações que usam a Integração VNet. 

![Diagrama que ilustra a utilização de pontos finais de serviço com o Gateway de Aplicação.](media/networking-features/service-endpoints-appgw.png)

Para saber mais sobre configurar os pontos finais do serviço com a sua aplicação, consulte [as restrições de acesso ao Serviço de Aplicações Azure.][serviceendpoints]

### <a name="private-endpoint"></a>Ponto Final Privado

Private Endpoint é uma interface de rede que o liga de forma privada e segura à sua Web App por link privado Azure. O Private Endpoint utiliza um endereço IP privado a partir da sua rede virtual, efetivamente trazendo a aplicação web para a sua rede virtual. Esta funcionalidade *destina-se* apenas aos fluxos de entrada para a sua aplicação web.
Para obter mais informações, consulte [utilizar pontos finais privados para a Azure Web App][privateendpoints].

Alguns usam casos para esta funcionalidade:

* Restringir o acesso à sua aplicação a partir de recursos numa rede virtual. 
* Exponha a sua aplicação num IP privado na sua rede virtual. 
* Proteja a sua aplicação com um WAF.

Os pontos finais privados impedem a exfiltração de dados porque a única coisa que pode alcançar através do ponto final privado é a app com a qual está configurada. 
 
### <a name="hybrid-connections"></a>Ligações Híbridas

As Conexões Híbridas do Serviço de Aplicações permitem que as suas aplicações façam chamadas *de saída* para pontos finais TCP especificados. O ponto final pode ser no local, numa rede virtual, ou em qualquer lugar que permita o tráfego de saída para Azure no porto 443. Para utilizar a funcionalidade, é necessário instalar um agente de retransmissão chamado Hybrid Connection Manager num Windows Server 2012 ou num anfitrião mais recente. O Hybrid Connection Manager precisa de ser capaz de chegar ao Azure Relay na porta 443. Você pode baixar Hybrid Connection Manager a partir do UI de Conexões Híbridas do Serviço de Aplicações no portal. 

![Diagrama que mostra o fluxo de rede de ligações híbridas.](media/networking-features/hybrid-connections.png)

As ligações híbridas do Serviço de Aplicações são construídas sobre a capacidade de conexões híbridas Azure Relay. O App Service utiliza uma forma especializada da funcionalidade que apenas suporta fazer chamadas de saída da sua app para um anfitrião e porta TCP. Este anfitrião e porta só precisam de ser resolvidos no anfitrião onde está instalado o Hybrid Connection Manager. 

Quando a aplicação, no Serviço de Aplicações, faz uma procura de DNS no anfitrião e na porta definida na sua ligação híbrida, o tráfego redireciona automaticamente para passar pela ligação híbrida e para fora do Hybrid Connection Manager. Para saber mais, consulte [as Conexões Híbridas do Serviço de Aplicações.][hybridconn]

Esta funcionalidade é comumente utilizada para:

* Aceder a recursos em redes privadas que não estejam ligadas ao Azure com uma VPN ou ExpressRoute.
* Apoie a migração de aplicações no local para o Serviço de Aplicações sem a necessidade de mover bases de dados de suporte.  
* Fornecer acesso com uma segurança melhorada a um único hospedeiro e porta por ligação híbrida. A maioria das funcionalidades de networking abrem acesso a uma rede. Com ligações híbridas, só pode chegar ao único hospedeiro e porta.
* Cenários de cobertura não abrangidos por outros métodos de conectividade de saída.
* Realize o desenvolvimento no Serviço de Aplicações de uma forma que permita às aplicações utilizarem facilmente recursos no local. 

Uma vez que esta funcionalidade permite o acesso a recursos no local sem um buraco de firewall de entrada, é popular entre os desenvolvedores. As outras funcionalidades de rede do Serviço de Aplicações de saída estão relacionadas com a Rede Virtual Azure. As Ligações Híbridas não dependem de passar por uma rede virtual. Pode ser usado para uma maior variedade de necessidades de networking. 

Note que as Ligações Híbridas do Serviço de Aplicações desconhecem o que está a fazer. Assim, pode usá-lo para aceder a uma base de dados, um serviço web ou uma tomada TCP arbitrária num computador central. A característica essencialmente túneis pacotes TCP. 

A Hybrid Connections é popular para o desenvolvimento, mas também é usada em aplicações de produção. É ótimo para aceder a um serviço web ou base de dados, mas não é apropriado para situações que envolvem criar muitas ligações. 

### <a name="gateway-required-vnet-integration"></a>Integração VNet exigida pelo Gateway 

A integração VNet do Serviço de Aplicações exigida pelo Gateway permite que a sua aplicação faça pedidos *de saída* numa rede virtual Azure. A funcionalidade funciona ligando o anfitrião a sua aplicação está a ser realizada numa porta de entrada de Rede Virtual na sua rede virtual, utilizando uma VPN ponto-a-local. Ao configurar a funcionalidade, a sua aplicação obtém um dos endereços ponto a local atribuídos a cada instância. Esta funcionalidade permite-lhe aceder a recursos em redes virtuais clássicas ou Azure Resource Manager em qualquer região. 

![Diagrama que ilustra a integração VNet exigida por gateways.](media/networking-features/gw-vnet-integration.png)

Esta funcionalidade resolve o problema de aceder a recursos noutras redes virtuais. Pode até ser usado para ligar através de uma rede virtual a outras redes virtuais ou no local. Não funciona com redes virtuais ligadas ao ExpressRoute, mas funciona com redes VPN ligadas ao site. Normalmente é inadequado utilizar esta funcionalidade a partir de uma aplicação num Ambiente de Serviço de Aplicações (ASE) porque o ASE já se encontra na sua rede virtual. Utilize casos para esta funcionalidade:

* Aceder a recursos em IPs privados nas suas redes virtuais Azure. 
* Aceda aos recursos no local se houver uma VPN local. 
* Aceder a recursos em redes virtuais. 

Quando esta funcionalidade estiver ativada, a sua aplicação utilizará o servidor DNS com o qual a rede virtual de destino está configurada. Para obter mais informações sobre esta funcionalidade, consulte [a Integração VNet do Serviço de Aplicações.][vnetintegrationp2s] 

### <a name="vnet-integration"></a>Integração de VNet

A integração VNet exigida pelo Gateway é útil, mas não resolve o problema de aceder a recursos através do ExpressRoute. Além de precisar estender as ligações ExpressRoute, há a necessidade de as aplicações poderem fazer chamadas para serviços protegidos pelo ponto final do serviço. Outra capacidade de Integração VNet pode atender a estas necessidades. 

A nova funcionalidade de Integração VNet permite-lhe colocar a parte de trás da sua aplicação numa sub-rede numa rede virtual do Resource Manager na mesma região que a sua aplicação. Esta funcionalidade não se encontra disponível a partir de um Ambiente de Serviço de Aplicações, que já se encontra numa rede virtual. Utilize casos para esta funcionalidade:

* Aceder a recursos em redes virtuais do Gestor de Recursos na mesma região.
* Recursos de acesso que são protegidos com pontos finais de serviço. 
* Recursos de acesso acessíveis através das ligações ExpressRoute ou VPN.
* Ajuda para proteger todo o tráfego de saída. 
* Túnel de força todo o tráfego de saída. 

![Diagrama que ilustra a Integração VNet.](media/networking-features/vnet-integration.png)

Para saber mais, consulte [a Integração VNet do Serviço de Aplicações.][vnetintegration]

### <a name="app-service-environment"></a>Ambiente do Serviço de Aplicações 

Um Ambiente de Serviço de Aplicações (ASE) é uma implantação de um único inquilino do Azure App Service que funciona na sua rede virtual. Alguns casos como este para esta característica:

* Aceda aos recursos na sua rede virtual.
* Aceder a recursos através do ExpressRoute.
* Exponha as suas aplicações com um endereço privado na sua rede virtual. 
* Aceder a recursos através dos pontos finais do serviço. 

Com um ASE, não precisa de usar funcionalidades como Integração VNet ou pontos finais de serviço porque o ASE já está na sua rede virtual. Se pretender aceder a recursos como SQL ou Azure Storage sobre os pontos finais do serviço, ative os pontos finais do serviço na sub-rede ASE. Se quiser aceder a recursos na rede virtual, não precisa de fazer nenhuma configuração adicional. Se quiser aceder a recursos através do ExpressRoute, já se encontra na rede virtual e não precisa de configurar nada sobre o ASE ou as aplicações que lhe estão. 

Como as aplicações num ILB ASE podem ser expostas num endereço IP privado, pode facilmente adicionar dispositivos WAF para expor apenas as aplicações que pretende para a internet e ajudar a manter o resto seguro. Esta funcionalidade pode ajudar a facilitar o desenvolvimento de aplicações multifunções. 

Algumas coisas não são atualmente possíveis a partir do serviço multitenant, mas são possíveis a partir de um ASE. Eis alguns exemplos:

* Exponha as suas aplicações num endereço IP privado.
* Ajude a proteger todo o tráfego de saída com controlos de rede que não fazem parte da sua aplicação.
* Hospedar as suas aplicações num serviço de inquilino único. 
* Dimensione até muitos mais casos do que são possíveis no serviço multitenant. 
* Carregue os certificados privados de clientes ca para uso pelas suas aplicações com pontos finais privados seguros da CA.
* Force o TLS 1.1 em todas as aplicações hospedadas no sistema sem qualquer capacidade de desativar a aplicação. 
* Forneça um endereço de saída dedicado para todas as aplicações do seu ASE que não sejam partilhadas com os clientes. 

![Diagrama que ilustra um ASE numa rede virtual.](media/networking-features/app-service-environment.png)

O ASE fornece a melhor história em torno de hospedagem de aplicações isoladas e dedicadas, mas envolve alguns desafios de gestão. Algumas coisas a ter em conta antes de utilizar um ASE operacional:
 
 * Um ASE corre dentro da sua rede virtual, mas tem dependências fora da rede virtual. Essas dependências devem ser permitidas. Para obter mais informações, consulte [considerações de Networking para um Ambiente de Serviço de Aplicações.][networkinfo]
 * Um ASE não escala imediatamente como o serviço multitenant. É preciso antecipar as necessidades de escala em vez de escalar reactivamente. 
 * Um ASE tem um custo adiantado mais elevado. Para tirar o máximo partido do seu ASE, deve planear colocar muitas cargas de trabalho numa ASE em vez de usá-la para pequenos esforços.
 * As aplicações num ASE não podem restringir seletivamente o acesso a algumas aplicações na ASE e não a outras.
 * Um ASE está numa sub-rede, e quaisquer regras de networking aplicam-se a todo o tráfego de e para esse ASE. Se quiser atribuir regras de tráfego de entrada para apenas uma aplicação, utilize restrições de acesso. 

## <a name="combining-features"></a>Combinar características 

As funcionalidades notadas para o serviço multitenant podem ser usadas em conjunto para resolver casos de utilização mais elaborados. Dois dos casos de uso mais comuns são descritos aqui, mas são apenas exemplos. Ao entender o que as várias funcionalidades fazem, você pode atender quase todas as necessidades de arquitetura do seu sistema.

### <a name="place-an-app-into-a-virtual-network"></a>Coloque uma aplicação numa rede virtual

Pode perguntar-se como colocar uma aplicação numa rede virtual. Se colocar a sua aplicação numa rede virtual, os pontos finais de entrada e saída da aplicação estão dentro da rede virtual. Um ASE é a melhor forma de resolver este problema. Mas pode atender a maioria das suas necessidades dentro do serviço multitenant combinando funcionalidades. Por exemplo, pode hospedar aplicações apenas intranet com endereços de entrada e saída privados:

* Criar uma porta de entrada de aplicações com endereços de entrada e saída privados.
* Garantir o tráfego de entrada na sua aplicação com pontos finais de serviço. 
* A utilização da nova funcionalidade de Integração VNet para que a parte traseira da sua aplicação esteja na sua rede virtual. 

Este estilo de implementação não lhe dará um endereço dedicado para tráfego de saída para a internet ou a capacidade de bloquear todo o tráfego de saída da sua aplicação. Vai dar-te muito do que só conseguirias com um ASE. 

### <a name="create-multitier-applications"></a>Criar aplicações multi-tiers

Uma aplicação multifunções é uma aplicação na qual as aplicações de back-end da API só podem ser acedidas a partir do nível frontal. Há duas formas de criar uma aplicação multi-mais. Ambos começam por utilizar a Integração VNet para ligar a sua aplicação web frontal a uma sub-rede numa rede virtual. Ao fazê-lo, a sua aplicação web irá fazer chamadas para a sua rede virtual. Depois de a sua aplicação frontal estar ligada à rede virtual, tem de decidir como bloquear o acesso à sua aplicação API. Pode:

* Hospedar tanto a frente como a app API no mesmo ILB ASE, e expor a aplicação frontal para a internet usando um gateway de aplicações.
* Hospedar a parte frontal no serviço multitenant e na parte traseira num ILB ASE.
* Hospedar tanto a frente como a app API no serviço multitenant.

Se você está hospedando a frente e a app API para uma aplicação multi-mais, você pode:

- Exponha a sua aplicação API utilizando pontos finais privados na sua rede virtual:

  ![Diagrama que ilustra o uso de pontos finais privados numa aplicação de dois níveis.](media/networking-features/multi-tier-app-private-endpoint.png)

- Utilize pontos finais de serviço para garantir que o tráfego de entrada para a sua aplicação API provém apenas da sub-rede utilizada pela sua aplicação web frontal:

  ![Diagrama que ilustra o uso de pontos finais de serviço para ajudar a garantir uma aplicação.](media/networking-features/multi-tier-app.png)

Aqui ficam algumas considerações para ajudá-lo a decidir que método usar:

* Quando utiliza pontos finais de serviço, só precisa de proteger o tráfego da sua aplicação API para a sub-rede de integração. Isto ajuda a proteger a aplicação da API, mas ainda pode ter exfiltração de dados da sua aplicação frontal para outras aplicações no serviço de aplicações.
* Quando utiliza pontos finais privados, tem duas sub-redes em jogo, o que acrescenta complexidade. Além disso, o ponto final privado é um recurso de alto nível e adiciona sobrecarga de gestão. O benefício da utilização de pontos finais privados é que não tem a possibilidade de exfiltração de dados. 

Qualquer um dos métodos funcionará com múltiplas extremidades dianteiras. Em pequena escala, os pontos finais de serviço são mais fáceis de usar porque simplesmente ativa os pontos finais de serviço para a aplicação API na sub-rede de integração frontal. À medida que adiciona mais aplicações frontais, precisa de ajustar todas as aplicações da API para incluir pontos finais de serviço com a sub-rede de integração. Quando usa pontos finais privados, há mais complexidade, mas não tem de alterar nada nas suas aplicações API depois de definir um ponto final privado. 

### <a name="line-of-business-applications"></a>Aplicações de linha de negócio

As aplicações line-of-business (LOB) são aplicações internas que normalmente não estão expostas para acesso a partir da internet. Estas aplicações são chamadas de dentro das redes corporativas onde o acesso pode ser estritamente controlado. Se utilizar um ILB ASE, é fácil hospedar as suas aplicações de linha de negócio. Se utilizar o serviço multitenant, pode utilizar pontos finais privados ou utilizar pontos finais de serviço combinados com um gateway de aplicação. Existem duas razões para usar um gateway de aplicação com pontos finais de serviço em vez de usar pontos finais privados:
* Precisa de proteção WAF nas suas aplicações LOB.
* Pretende carregar o equilíbrio em várias instâncias das suas aplicações LOB.

Se nenhuma destas necessidades se aplicar, é melhor usar pontos finais privados. Com pontos finais privados disponíveis no Serviço de Aplicações, pode expor as suas aplicações em endereços privados na sua rede virtual. O ponto final privado que coloca na sua rede virtual pode ser alcançado através das ligações ExpressRoute e VPN. 

Configurar os pontos finais privados irá expor as suas aplicações num endereço privado, mas terá de configurar o DNS para chegar a esse endereço a partir do local. Para que esta configuração funcione, terá de encaminhar a zona privada Azure DNS que contém os seus pontos finais privados para os servidores DNS no local. As zonas privadas de Azure DNS não suportam o encaminhamento de zonas, mas pode suportar o encaminhamento da zona utilizando um servidor DNS para o efeito. O modelo [DNS Forwarder](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) facilita o encaminhamento da sua zona privada Azure DNS para os servidores DNS no local.

## <a name="app-service-ports"></a>Portas de Serviço de Aplicações

Se pesquisar o Serviço de Aplicações, encontrará várias portas expostas para ligações de entrada. Não há como bloquear ou controlar o acesso a estas portas no serviço multitenant. Aqui está a lista de portas expostas:

| Utilização | Portos ou portos |
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
