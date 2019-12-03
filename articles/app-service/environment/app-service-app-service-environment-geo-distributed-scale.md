---
title: Escala distribuída geográfica
description: Saiba como dimensionar horizontalmente os aplicativos usando a distribuição geográfica com os ambientes do Gerenciador de tráfego e do serviço de aplicativo.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688805"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Escala Distribuída Geograficamente com Ambientes de Serviço de Aplicações
## <a name="overview"></a>Visão geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cenários de aplicativos que exigem escala muito alta podem exceder a capacidade de recursos de computação disponível para uma única implantação de um aplicativo.  Os aplicativos de votação, eventos esportivos e eventos de entretenimento televisionados são exemplos de cenários que exigem escala extremamente alta. Os requisitos de alta escala podem ser atendidos por meio da expansão horizontal de aplicativos, com várias implantações de aplicativo sendo feitas em uma única região, bem como entre regiões, para lidar com requisitos de carga extrema.

Ambientes de serviço de aplicativo são uma plataforma ideal para expansão horizontal.  Depois que uma configuração de Ambiente do Serviço de Aplicativo tiver sido selecionada que possa dar suporte a uma taxa de solicitação conhecida, os desenvolvedores poderão implantar ambientes de serviço de aplicativo adicionais no modo "cookie cortador" para obter uma capacidade de carga de pico desejada.

Por exemplo, suponha que um aplicativo em execução em uma configuração de Ambiente do Serviço de Aplicativo tenha sido testado para manipular solicitações de 20 mil por segundo (RPS).  Se a capacidade de carga de pico desejada for de 100 mil RPS, cinco (5) ambientes de serviço de aplicativo poderão ser criados e configurados para garantir que o aplicativo possa lidar com a carga projetada máxima.

Como os clientes normalmente acessam aplicativos usando um domínio personalizado (ou intuitivo), os desenvolvedores precisam de uma maneira de distribuir solicitações de aplicativo em todas as instâncias de Ambiente do Serviço de Aplicativo.  Uma ótima maneira de fazer isso é resolver o domínio personalizado usando um [perfil do Gerenciador de tráfego do Azure][AzureTrafficManagerProfile].  O perfil do Gerenciador de tráfego pode ser configurado para apontar para todos os ambientes individuais do serviço de aplicativo.  O Traffic Manager lidará automaticamente com a distribuição de clientes em todos os ambientes do serviço de aplicativo com base nas configurações de balanceamento de carga no perfil do Gerenciador de tráfego.  Essa abordagem funciona independentemente de todos os ambientes do serviço de aplicativo serem implantados em uma única região do Azure ou implantados em todo o mundo em várias regiões do Azure.

Além disso, como os clientes acessam aplicativos por meio do domínio intuitivo, os clientes não sabem do número de ambientes de serviço de aplicativo que executam um aplicativo.  Como resultado, os desenvolvedores podem adicionar e remover com rapidez e facilidade ambientes de serviço de aplicativo com base na carga de tráfego observada.

O diagrama conceitual abaixo descreve um aplicativo dimensionado horizontalmente em três ambientes de serviço de aplicativo em uma única região.

![Arquitetura conceitual][ConceptualArchitecture] 

O restante deste tópico percorre as etapas envolvidas na configuração de uma topologia distribuída para o aplicativo de exemplo usando vários ambientes de serviço de aplicativo.

## <a name="planning-the-topology"></a>Planejando a topologia
Antes de criar uma superfície de aplicativo distribuído, ele ajuda a ter algumas informações com antecedência.

* **Domínio personalizado para o aplicativo:**  Qual é o nome de domínio personalizado que os clientes usarão para acessar o aplicativo?  Para o aplicativo de exemplo, o nome de domínio personalizado é `www.scalableasedemo.com`
* **Domínio do Gerenciador de tráfego:**  Um nome de domínio precisa ser escolhido ao criar um [perfil do Gerenciador de tráfego do Azure][AzureTrafficManagerProfile].  Esse nome será combinado com o sufixo *trafficmanager.net* para registrar uma entrada de domínio que é gerenciada pelo Gerenciador de tráfego.  Para o aplicativo de exemplo, o nome escolhido é *Scalable-ase-demo*.  Como resultado, o nome de domínio completo que é gerenciado pelo Gerenciador de tráfego é *Scalable-ase-demo.trafficmanager.net*.
* **Estratégia para dimensionar a superfície do aplicativo:**  A superfície do aplicativo será distribuída entre vários ambientes de serviço de aplicativo em uma única região?  Várias regiões?  Uma combinação e uma correspondência de ambas as abordagens?  A decisão deve ser baseada em expectativas de onde o tráfego do cliente será originado, assim como o restante da infra-estrutura de back-end de suporte de um aplicativo pode ser dimensionada.  Por exemplo, com um aplicativo de 100% sem monitoração de estado, um aplicativo pode ser amplamente dimensionado usando uma combinação de vários ambientes de serviço de aplicativo por região do Azure, multiplicado por ambientes de serviço de aplicativo implantados em várias regiões do Azure.  Com mais de 15 regiões públicas do Azure disponíveis para escolher, os clientes podem realmente criar uma superfície de aplicativo de hiperescala em todo o mundo.  Para o aplicativo de exemplo usado neste artigo, três ambientes de serviço de aplicativo foram criados em uma única região do Azure (EUA Central do Sul).
* **Convenção de nomenclatura para os ambientes do serviço de aplicativo:**  Cada Ambiente do Serviço de Aplicativo requer um nome exclusivo.  Além de um ou dois ambientes de serviço de aplicativo, é útil ter uma Convenção de nomenclatura para ajudar a identificar cada Ambiente do Serviço de Aplicativo.  Para o aplicativo de exemplo, foi usada uma Convenção de nomenclatura simples.  Os nomes dos três ambientes de serviço de aplicativo são *fe1ase*, *fe2ase*e *fe3ase*.
* **Convenção de nomenclatura para os aplicativos:**  Como várias instâncias do aplicativo serão implantadas, um nome será necessário para cada instância do aplicativo implantado.  Um recurso pouco conhecido, mas muito conveniente, dos ambientes do serviço de aplicativo é que o mesmo nome de aplicativo pode ser usado em vários ambientes de serviço de aplicativo.  Como cada Ambiente do Serviço de Aplicativo tem um sufixo de domínio exclusivo, os desenvolvedores podem optar por usar novamente o mesmo nome de aplicativo em cada ambiente.  Por exemplo, um desenvolvedor poderia ter aplicativos chamados da seguinte maneira: *MyApp.foo1.p.azurewebsites.net*, *MyApp.Foo2.p.azurewebsites.net*, *MyApp.foo3.p.azurewebsites.net*, etc.  Para o aplicativo de exemplo, embora cada instância de aplicativo também tenha um nome exclusivo.  Os nomes de instância de aplicativo usados são *webfrontend1*, *webfrontend2*e *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configurando o perfil do Gerenciador de tráfego
Depois que várias instâncias de um aplicativo são implantadas em vários ambientes do serviço de aplicativo, as instâncias individuais do aplicativo podem ser registradas com o Gerenciador de tráfego.  Para o aplicativo de exemplo, um perfil do Gerenciador de tráfego é necessário para *Scalable-ase-demo.trafficmanager.net* que pode rotear clientes para qualquer uma das seguintes instâncias de aplicativo implantadas:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Uma instância do aplicativo de exemplo implantada no primeiro Ambiente do Serviço de Aplicativo.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Uma instância do aplicativo de exemplo implantada na segunda Ambiente do Serviço de Aplicativo.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Uma instância do aplicativo de exemplo implantada no terceiro Ambiente do Serviço de Aplicativo.

A maneira mais fácil de registrar vários pontos de extremidade de serviço Azure App, todos em execução na **mesma** região do Azure, é com o [suporte ao Gerenciador de tráfego][ARMTrafficManager]do PowerShell Azure Resource Manager.  

A primeira etapa é criar um perfil do Gerenciador de tráfego do Azure.  O código a seguir mostra como o perfil foi criado para o aplicativo de exemplo:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Observe como o parâmetro *RelativeDnsName* foi definido como *Scalable-ase-demo*.  É assim que o nome de domínio *Scalable-ase-demo.trafficmanager.net* é criado e associado a um perfil do Gerenciador de tráfego.

O parâmetro *TrafficRoutingMethod* define a política de balanceamento de carga que o Gerenciador de tráfego usará para determinar como espalhar a carga do cliente em todos os pontos de extremidade disponíveis.  Neste exemplo, o método *ponderado* foi escolhido.  Isso fará com que as solicitações do cliente sejam distribuídas em todos os pontos de extremidade do aplicativo registrado com base nos pesos relativos associados a cada ponto de extremidades. 

Com o perfil criado, cada instância de aplicativo é adicionada ao perfil como um ponto de extremidade do Azure nativo.  O código a seguir busca uma referência a cada aplicativo Web de front-end e, em seguida, adiciona cada aplicativo como um ponto de extremidade do Gerenciador de tráfego por meio do parâmetro *TargetResourceId* .

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Observe como há uma chamada para *Add-AzureTrafficManagerEndpointConfig* para cada instância de aplicativo individual.  O parâmetro *TargetResourceId* em cada comando do PowerShell faz referência a uma das três instâncias de aplicativo implantadas.  O perfil do Gerenciador de tráfego espalhará a carga entre todos os três pontos de extremidade registrados no perfil.

Todos os três pontos de extremidade usam o mesmo valor (10) para o parâmetro *Weight* .  Isso resulta na difusão do Gerenciador de tráfego de solicitações de clientes entre todas as três instâncias de aplicativo de maneira relativamente uniforme. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Apontando o domínio personalizado do aplicativo no domínio do Traffic Manager
A etapa final necessária é apontar o domínio personalizado do aplicativo no domínio do Traffic Manager.  Para o aplicativo de exemplo, isso significa apontar `www.scalableasedemo.com` em `scalable-ase-demo.trafficmanager.net`.  Essa etapa precisa ser concluída com o registrador de domínio que gerencia o domínio personalizado.  

Usando as ferramentas de gerenciamento de domínio do registrador, é necessário criar registros CNAME que apontam o domínio personalizado no domínio do Traffic Manager.  A figura abaixo mostra um exemplo de como essa configuração de CNAME é semelhante a:

![CNAME para domínio personalizado][CNAMEforCustomDomain] 

Embora não seja abordado neste tópico, lembre-se de que cada instância de aplicativo individual precisa ter o domínio personalizado registrado com ele também.  Caso contrário, se uma solicitação a fizer em uma instância de aplicativo e o aplicativo não tiver o domínio personalizado registrado com o aplicativo, a solicitação falhará.  

Neste exemplo, o domínio personalizado é `www.scalableasedemo.com`e cada instância do aplicativo tem o domínio personalizado associado a ele.

![Domínio Personalizado][CustomDomain] 

Para uma recapitulação do registro de um domínio personalizado com aplicativos de serviço Azure App, consulte o seguinte artigo sobre como [registrar domínios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Experimentando a topologia distribuída
O resultado final do Gerenciador de tráfego e da configuração de DNS é que as solicitações para `www.scalableasedemo.com` fluirão através da seguinte sequência:

1. Um navegador ou dispositivo fará uma pesquisa de DNS para `www.scalableasedemo.com`
2. A entrada CNAME no registrador de domínio faz com que a pesquisa de DNS seja redirecionada para o Gerenciador de tráfego do Azure.
3. Uma pesquisa DNS é feita para *Scalable-ase-demo.trafficmanager.net* em um dos servidores DNS do Gerenciador de tráfego do Azure.
4. Com base na política de balanceamento de carga (o parâmetro *TrafficRoutingMethod* usado anteriormente ao criar o perfil do Gerenciador de tráfego), o Gerenciador de tráfego selecionará um dos pontos de extremidade configurados e retornará o FQDN desse ponto para o navegador ou dispositivo.
5. Como o FQDN do ponto de extremidade é a URL de uma instância de aplicativo em execução em um Ambiente do Serviço de Aplicativo, o navegador ou dispositivo solicitará que um servidor DNS Microsoft Azure resolva o FQDN para um endereço IP. 
6. O navegador ou dispositivo enviará a solicitação HTTP/S para o endereço IP.  
7. A solicitação chegará em uma das instâncias do aplicativo em execução em um dos ambientes do serviço de aplicativo.

A imagem de console abaixo mostra uma pesquisa de DNS para o domínio personalizado do aplicativo de exemplo resolvendo com êxito uma instância de aplicativo em execução em um dos três ambientes de serviço de aplicativo de exemplo (neste caso, o segundo dos três ambientes de serviço de aplicativo):

![Pesquisa de DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Links e informações adicionais
Documentação sobre o [suporte do Gerenciador de tráfego do Azure Resource Manager][ARMTrafficManager]PowerShell.  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
