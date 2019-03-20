---
title: Dimensionamento distribuído geograficamente com ambientes de serviço de aplicações - Azure
description: Saiba como dimensionar horizontalmente aplicações com a distribuição geográfica com o Gestor de tráfego e ambientes de serviço de aplicações.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 769e6b9936ad6d3cb963e208cec4c49813f2b6d3
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188327"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Escala Distribuída Geograficamente com Ambientes de Serviço de Aplicações
## <a name="overview"></a>Descrição geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cenários de aplicações que requerem dimensionamento muito elevado podem exceder a capacidade de recursos de computação disponível para uma implementação única de uma aplicação.  Votações, eventos e eventos de entretenimento televised são todos os exemplos de cenários que exigem dimensionamento extremamente alto. Podem ser cumpridos os requisitos de escala elevada ao dimensionar horizontalmente as aplicações, com várias implementações de aplicações que está a ser efetuadas numa única região, bem como em várias regiões, para lidar com requisitos de carga extrema.

Os ambientes de serviço de aplicações são uma plataforma ideal para aumentar horizontalmente.  Uma vez uma aplicação ambiente de serviço de configuração tiver sido selecionada que pode oferecer suporte a uma taxa de pedidos conhecidos, os programadores podem implementar adicionais ambientes de serviço de aplicações de modo de "cortador de biscoitos" para obter uma capacidade de carga de pico pretendido.

Por exemplo, suponha uma aplicação em execução numa configuração de ambiente de serviço de aplicações foi testada para lidar com 20 mil pedidos por segundo (RPS).  Se a capacidade de carga de pico pretendido for 100 mil RPS, em seguida, cinco (5) ambientes do App Service podem ser criadas e configuradas para garantir que a aplicação pode processar a carga máxima prevista.

Uma vez que os clientes normalmente aceder a aplicações através de um domínio personalizado (ou personalizado), os desenvolvedores precisam de uma forma de distribuir os pedidos de aplicação em todas as instâncias de ambiente de serviço de aplicações.  Uma excelente forma de fazer isso é resolver o domínio personalizado, utilizando uma [perfil do Gestor de tráfego do Azure][AzureTrafficManagerProfile].  O perfil do Gestor de tráfego pode ser configurado para apontar para todos os ambientes de serviço de aplicações individuais.  O Gestor de tráfego manipula automaticamente os clientes de distribuição em todos os ambientes de serviço de aplicações com base nas definições do perfil do Gestor de tráfego de balanceamento de carga.  Essa abordagem funciona independentemente se todos os ambientes de serviço de aplicações são implementados numa única região do Azure ou em todo o mundo implementados em várias regiões do Azure.

Além disso, uma vez que os clientes aceder às aplicações através do domínio intuitivo, os clientes não estão cientes do número de ambientes de serviço de aplicações a executar uma aplicação.  Assim os desenvolvedores podem rápida e facilmente adicionar e remover, ambientes de serviço de aplicações com base na carga de tráfego observado.

O diagrama conceptual abaixo ilustra uma aplicação horizontalmente aumentada horizontalmente em três ambientes de serviço de aplicações numa única região.

![Arquitetura conceptual][ConceptualArchitecture] 

O resto deste tópico descreve as etapas envolvidas com a configuração de uma topologia distribuída para a aplicação de exemplo usando vários ambientes de serviço de aplicações.

## <a name="planning-the-topology"></a>Planejar a topologia
Antes de criar um espaço de aplicação distribuída, ajuda para ter algumas informações de partes antes do tempo.

* **Domínio personalizado para a aplicação:**  O que é o nome de domínio personalizado que os clientes irão utilizar para aceder à aplicação?  Para a aplicação de exemplo é o nome de domínio personalizado `www.scalableasedemo.com`
* **Domínio do Gestor de tráfego:**  Um nome de domínio tem de ser escolhido ao criar uma [perfil do Gestor de tráfego do Azure][AzureTrafficManagerProfile].  Este nome será combinado com o *trafficmanager.net* sufixo para registar uma entrada de domínio que é gerenciada pelo Gestor de tráfego.  Para a aplicação de exemplo, o escolhido de nome é *demonstração dimensionável ase*.  Como resultado é o nome de domínio completo que é gerido pelo Gestor de tráfego *demo.trafficmanager.net dimensionável ase*.
* **Estratégia para dimensionar os requisitos de espaço de aplicação:**  A superfície do aplicativo ser distribuída em vários ambientes de serviço de aplicações numa única região?  Várias regiões?  Um misturar e combinar as duas abordagens?  A decisão deve basear-se as expectativas de onde o tráfego do cliente terão origem, bem como a eficiência com que pode dimensionar o restante de uma aplicação que suporta a infraestrutura de back-end.  Por exemplo, com uma aplicação sem monitoração de estado de 100%, uma aplicação pode ser altamente dimensionada utilizando uma combinação de vários ambientes de serviço de aplicações por região do Azure, multiplicado por ambientes de serviço de aplicação implementado em várias regiões do Azure.  Com 15 + regiões públicas do Azure disponíveis para escolha, os clientes verdadeiramente podem criar um volume de memória do aplicativo de hiper escala a nível mundial.  Para a aplicação de exemplo utilizada neste artigo, três ambientes de serviço de aplicações foram criados numa única região do Azure (Sul E.u.a.).
* **Convenção de nomenclatura para os ambientes de serviço de aplicações:**  Cada ambiente de serviço de aplicações requer um nome exclusivo.  Para além de um ou dois ambientes de serviço de aplicações é útil ter uma convenção de nomenclatura para ajudar a identificar cada ambiente de serviço de aplicações.  Para a aplicação de exemplo foi utilizada uma convenção de nomenclatura simple.  Os nomes dos três ambientes de serviço de aplicação são *fe1ase*, *fe2ase*, e *fe3ase*.
* **Convenção de nomenclatura para as aplicações:**  Uma vez que várias instâncias da aplicação serão implementadas, é necessário um nome para cada instância da aplicação implementada.  Um recurso pouco conhecido, mas muito conveniente de ambientes de serviço de aplicações é que o mesmo nome de aplicação pode ser usado em vários ambientes de serviço de aplicações.  Uma vez que cada ambiente de serviço de aplicações tem um sufixo de domínio exclusivo, os desenvolvedores podem optar por utilizar novamente o mesmo nome de aplicação em cada ambiente.  Por exemplo um desenvolvedor pode ter aplicações com o nome da seguinte forma: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc.  Embora o aplicativo de exemplo de cada instância da aplicação também tem um nome exclusivo.  Os nomes de instância da aplicação utilizados são *webfrontend1*, *webfrontend2*, e *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Como configurar o perfil do Gestor de tráfego
Depois de várias instâncias de uma aplicação estão implementadas em vários ambientes de serviço de aplicações, as instâncias de aplicações individuais podem ser registadas com o Gestor de tráfego.  Para a aplicação de exemplo, um Gestor de tráfego de perfil é necessária para *demo.trafficmanager.net dimensionável ase* que possa rotear os clientes a qualquer uma das seguintes instâncias de aplicação implementada:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Uma instância da aplicação de exemplo implementada num ambiente de serviço de aplicações primeiro.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Uma instância da aplicação de exemplo implementada num ambiente de serviço de aplicações segundo.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Uma instância da aplicação de exemplo implementada num ambiente de serviço de aplicações terceiro.

A maneira mais fácil para registrar vários App Service do Azure pontos de extremidade, tudo em execução no **mesmo** região do Azure, é com o Powershell [suporte do Gestor de tráfego do Azure Resource Manager] [ ARMTrafficManager].  

A primeira etapa é criar um perfil do Gestor de tráfego do Azure.  O código abaixo mostra como o perfil foi criado para a aplicação de exemplo:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Observe como o *RelativeDnsName* parâmetro foi definido como *demonstração dimensionável ase*.  Isso é como o nome de domínio *demo.trafficmanager.net dimensionável ase* é criado e associado a um perfil do Gestor de tráfego.

O *TrafficRoutingMethod* parâmetro define a política de Gestor de tráfego irá utilizar para determinar como distribuir a carga de cliente em todos os pontos finais disponíveis de balanceamento de carga.  Neste exemplo o *ponderado* foi escolhido o método.  Isto irá resultar em pedidos de cliente que está a ser distribuídos por todos os pontos de extremidade do aplicativo registrado com base nos pesos relativos associados a cada ponto de extremidade. 

Com o perfil que criou, cada instância da aplicação é adicionada ao perfil como um ponto de final do Azure nativo.  O código a seguir obtém uma referência para cada aplicação de web de front-end e, em seguida, adiciona cada aplicação como um ponto de final do Gestor de tráfego por meio dos *TargetResourceId* parâmetro.

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Observe como não há uma chamada para *Add-AzureTrafficManagerEndpointConfig* para cada instância de aplicações individuais.  O *TargetResourceId* parâmetro em cada comando do Powershell faz referência a uma das três instâncias de aplicações implementada.  O perfil do Gestor de tráfego irá distribuir a carga por todos os três pontos de extremidade registrados no perfil.

Todos os três pontos de extremidade usam o mesmo valor (10) para o *peso* parâmetro.  Isso resulta em pedidos de cliente de distribuição Gestor de tráfego em todas as instâncias da aplicação de três relativamente uniformemente. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Apontar o domínio personalizado da aplicação no domínio do Gestor de tráfego
É a etapa final necessária para apontar o domínio personalizado da aplicação no domínio do Gestor de tráfego.  Para a aplicação de exemplo isso significa que aponte `www.scalableasedemo.com` em `scalable-ase-demo.trafficmanager.net`.  Este passo tem de ser concluído com a entidade de registo do domínio que gere o domínio personalizado.  

Usando ferramentas de gerenciamento de domínio de sua entidade de registo, um CNAME regista tem de ser criado que aponta o domínio personalizado, o domínio do Gestor de tráfego.  A figura a seguir mostra um exemplo do aspeto esta configuração de CNAME:

![CNAME para o domínio personalizado][CNAMEforCustomDomain] 

Embora não abrangido neste tópico, lembre-se de que cada instância de aplicação individual tem de ter o domínio personalizado registrado com ele também.  Caso contrário, se um pedido torna-o para uma instância de aplicação e o aplicativo não tem o domínio personalizado registado com a aplicação, o pedido irá falhar.  

Neste exemplo, o domínio personalizado é `www.scalableasedemo.com`, e cada instância da aplicação tem o domínio personalizado associado a ele.

![Domínio Personalizado][CustomDomain] 

Para recapitular de registrar um domínio personalizado com as aplicações do App Service do Azure, veja o seguinte artigo no [registo de domínios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Experimentar a topologia distribuída
O resultado final da configuração do Gestor de tráfego e o DNS é que os pedidos para `www.scalableasedemo.com` irá fluir pela seguinte sequência:

1. Um browser ou dispositivo fará com que uma pesquisa de DNS `www.scalableasedemo.com`
2. A entrada CNAME na entidade de registo da domínio faz com que a pesquisa DNS ser redirecionado para o Gestor de tráfego do Azure.
3. Uma pesquisa de DNS é feita *demo.trafficmanager.net dimensionável ase* em relação a um dos servidores de DNS do Gestor de tráfego do Azure.
4. Com base na política de balanceamento de carga (a *TrafficRoutingMethod* parâmetro utilizado anteriormente ao criar o perfil do Gestor de tráfego), o Gestor de tráfego irá selecionar um dos pontos finais configurados e retornar o FQDN do ponto de extremidade para o browser ou dispositivo.
5. Uma vez que o FQDN do ponto final é o Url de uma instância de aplicação em execução num ambiente de serviço de aplicações, o browser ou dispositivo irá pedir um servidor de DNS do Azure da Microsoft para resolver o FQDN para um endereço IP. 
6. O browser ou dispositivo irá enviar o pedido HTTP/S para o endereço IP.  
7. O pedido vai deparar-se em uma das instâncias de aplicação em execução dos ambientes do serviço de aplicações.

A imagem de consola abaixo mostra uma pesquisa de DNS para o domínio personalizado da aplicação de exemplo com êxito a resolver para uma instância de aplicação em execução em um a três ambientes de serviço de aplicações de exemplo (no caso do segundo os três ambientes de serviço de aplicação):

![Pesquisa de DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Links adicionais e informações
Documentação sobre o Powershell [suporte do Gestor de tráfego do Azure Resource Manager][ARMTrafficManager].  

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
