---
title: Escala geo distribuída
description: Aprenda a escalar horizontalmente aplicações usando geodistribuição com Traffic Manager e App Service Environments.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74688805"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Escala Distribuída Geograficamente com Ambientes de Serviço de Aplicações
## <a name="overview"></a>Descrição geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cenários de aplicação que requerem uma escala muito elevada podem exceder a capacidade de recursos computacionais disponíveis para uma única implementação de uma app.  As candidaturas de voto, eventos desportivos e eventos de entretenimento televisivo são todos exemplos de cenários que requerem uma escala extremamente elevada. Os requisitos de alta escala podem ser cumpridos através da escala horizontal de aplicações, com múltiplas implementações de aplicações a serem feitas numa única região, bem como em todas as regiões, para lidar com requisitos de carga extremas.

Os Ambientes de Serviço de Aplicações são uma plataforma ideal para a escala horizontal.  Uma vez selecionada uma configuração do App Service Environment que pode suportar uma taxa de pedido conhecida, os desenvolvedores podem implementar ambientes adicionais de serviço de aplicação em forma de "cortador de cookies" para alcançar uma capacidade de carga máxima desejada.

Por exemplo, suponha que uma aplicação em execução numa configuração do App Service Environment foi testada para lidar com pedidos de 20K por segundo (RPS).  Se a capacidade de carga máxima desejada for de 100K RPS, então cinco (5) Ambientes de Serviço de Aplicações podem ser criados e configurados para garantir que a aplicação pode lidar com a carga máxima projetada.

Uma vez que os clientes normalmente acedem a apps usando um domínio personalizado (ou vaidade), os desenvolvedores precisam de uma forma de distribuir pedidos de aplicações em todas as instâncias do App Service Environment.  Uma ótima maneira de o conseguir é resolver o domínio personalizado usando um perfil do Gestor de [Tráfego Azure.][AzureTrafficManagerProfile]  O perfil do Gestor de Tráfego pode ser configurado para apontar em todos os ambientes individuais do Serviço de Aplicações.  O Traffic Manager irá lidar automaticamente com a distribuição de clientes em todos os Ambientes de Serviço de Aplicação com base nas definições de equilíbrio de carga no perfil do Gestor de Tráfego.  Esta abordagem funciona independentemente de todos os Ambientes de Serviço saem implantados numa única região do Azure, ou implantados em todo o mundo em várias regiões do Azure.

Além disso, uma vez que os clientes acedem a apps através do domínio da vaidade, os clientes desconhecem o número de Ambientes de Serviço de Aplicações que executam uma aplicação.  Como resultado, os desenvolvedores podem adicionar e remover de forma rápida e fácil, ambientes de serviço de aplicação com base na carga de tráfego observada.

O diagrama conceptual abaixo retrata uma aplicação horizontalmente escalada através de três Ambientes de Serviço de Aplicações numa única região.

![Arquitetura Conceptual][ConceptualArchitecture] 

O restante deste tópico percorre os passos envolvidos na criação de uma topologia distribuída para a aplicação de amostra suster vários Ambientes de Serviço de Aplicações.

## <a name="planning-the-topology"></a>Planejamento da Topologia
Antes de construir uma pegada de aplicativo distribuída, ajuda a ter algumas informações de peças com antecedência.

* **Domínio personalizado para a aplicação:**  Qual é o nome de domínio personalizado que os clientes vão usar para aceder à aplicação?  Para a aplicação de amostra, o nome de domínio personalizado é`www.scalableasedemo.com`
* **Domínio do Gestor de Tráfego:**  Um nome de domínio precisa de ser escolhido na criação de um perfil de Gestor de [Tráfego Azure][AzureTrafficManagerProfile].  Este nome será combinado com o *sufixo trafficmanager.net* para registar uma entrada de domínio que é gerida pelo Gestor de Tráfego.  Para a aplicação de amostra, o nome escolhido é *escalável-ase-demo*.  Como resultado, o nome de domínio completo que é gerido pelo Traffic Manager é *scalable-ase-demo.trafficmanager.net*.
* **Estratégia para escalar a pegada da aplicação:**  A pegada da aplicação será distribuída por vários Ambientes de Serviços de Aplicações numa única região?  Várias regiões?  Uma mistura e correspondência de ambas as abordagens?  A decisão deve basear-se nas expectativas de onde o tráfego de clientes irá originar, bem como como o resto da infraestrutura de apoio de uma aplicação pode escalar.  Por exemplo, com uma aplicação 100% apátrida, uma aplicação pode ser massivamente dimensionada usando uma combinação de múltiplos ambientes de serviço de aplicação por região azure, multiplicado por Ambientes de Serviço de Aplicações implantados em várias regiões do Azure.  Com mais de 15 regiões públicas do Azure disponíveis para escolher, os clientes podem realmente construir uma pegada de aplicação em escala mundial.  Para a aplicação de amostras utilizada para este artigo, três Ambientes de Serviço sacar foram criados numa única região do Azure (Centro-Sul dos EUA).
* **Convenção de nomeação para os Ambientes de Serviço de Aplicações:**  Cada ambiente de serviço de aplicações requer um nome único.  Além de um ou dois Ambientes de Serviço de Aplicações, é útil ter uma convenção de nomeação para ajudar a identificar cada ambiente de serviço de aplicação.  Para a aplicação de amostra foi utilizada uma convenção de nomeação simples.  Os nomes dos três Ambientes de Serviço de Aplicação são *fe1ase,* *fe2ase,* e *fe3ase.*
* **Convenção de nomeação para as aplicações:**  Uma vez que vários casos da aplicação serão implementados, é necessário um nome para cada instância da aplicação implementada.  Uma característica pouco conhecida mas muito conveniente do App Service Environments é que o mesmo nome de aplicação pode ser usado em vários Ambientes de Serviço de Aplicações.  Uma vez que cada app service Environment tem um sufixo de domínio único, os desenvolvedores podem optar por reutilizar o mesmo nome de aplicação em cada ambiente.  Por exemplo, um desenvolvedor poderia ter aplicações com o seguinte nome: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net,* *myapp.foo3.p.azurewebsites.net,* etc.  Para a aplicação de amostras, embora cada instância de aplicação também tenha um nome único.  Os nomes da instância da aplicação utilizados são *webfrontend1*, *webfrontend2*, e *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configuração do perfil do gestor de tráfego
Uma vez que várias instâncias de uma aplicação são implementadas em vários Ambientes de Serviço de Aplicações, as instâncias individuais de aplicações podem ser registadas com o Traffic Manager.  Para a aplicação de amostra, é necessário um perfil do Traffic Manager para *scalable-ase-demo.trafficmanager.net* que possa direcionar os clientes para qualquer uma das seguintes instâncias de aplicações implementadas:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Uma instância da aplicação de amostra seleção implementada no primeiro Ambiente de Serviço de Aplicações.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Uma instância da aplicação de amostra seleção implantada no segundo App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Uma instância da aplicação de amostra seleção implantada no terceiro App Service Environment.

A forma mais fácil de registar vários pontos finais do Serviço de Aplicações Azure, todos em execução na **mesma** região do Azure, é com o suporte do [Powershell Azure Resource Manager][ARMTrafficManager]de Tráfego.  

O primeiro passo é criar um perfil de Gestor de Tráfego Azure.  O código abaixo mostra como o perfil foi criado para a aplicação da amostra:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Note como o parâmetro *RelativoDnsName* foi definido para *escalável-ase-demo*.  É assim que o nome de domínio *scalable-ase-demo.trafficmanager.net* é criado e associado a um perfil de Gestor de Tráfego.

O parâmetro *TrafficRoutingMethod* define a política de equilíbrio de carga que o Traffic Manager utilizará para determinar como difundir a carga do cliente em todos os pontos finais disponíveis.  Neste exemplo, foi escolhido o método *Ponderado.*  Isto resultará na distribuição de pedidos de clientes em todos os pontos finais da aplicação registada com base nos pesos relativos associados a cada ponto final. 

Com o perfil criado, cada instância de aplicação é adicionada ao perfil como um ponto final nativo do Azure.  O código abaixo adquire uma referência a cada aplicação frontal frontal web e, em seguida, adiciona cada aplicação como ponto final do Gestor de Tráfego através do parâmetro *TargetResourceId.*

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Note como há uma chamada para *Add-AzureTrafficManagerEndpointConfig* para cada instância de aplicação individual.  O parâmetro *TargetResourceId* em cada comando Powershell refere uma das três instâncias de aplicações implementadas.  O perfil do Gestor de Tráfego irá espalhar a carga pelos três pontos finais registados no perfil.

Todos os três pontos finais utilizam o mesmo valor (10) para o parâmetro *peso.*  Isto resulta em Traffic Manager a espalhar pedidos de clientes em todas as três instâncias de aplicações de forma relativamente uniforme. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Apontando o domínio personalizado da app para o domínio do gestor de tráfego
O passo final necessário é apontar o domínio personalizado da app para o domínio do Traffic Manager.  Para a aplicação `www.scalableasedemo.com` de `scalable-ase-demo.trafficmanager.net`amostra, isto significa apontar para .  Este passo tem de ser concluído com o registo de domínio que gere o domínio personalizado.  

Utilizando as ferramentas de gestão de domínio do seu registo, é necessário criar um registo CNAME que aponte o domínio personalizado no domínio do Traffic Manager.  A imagem abaixo mostra um exemplo de como esta configuração CNAME se parece:

![CNAME para Domínio Personalizado][CNAMEforCustomDomain] 

Apesar de não estar abrangida por este tópico, lembre-se que cada instância de aplicação individual precisa de ter o domínio personalizado registado com ele também.  Caso contrário, se um pedido chegar a uma instância de aplicação, e a aplicação não tiver o domínio personalizado registado na aplicação, o pedido falhará.  

Neste exemplo, o `www.scalableasedemo.com`domínio personalizado é , e cada instância de aplicação tem o domínio personalizado associado a ele.

![Domínio Personalizado][CustomDomain] 

Para uma recapitulação do registo de um domínio personalizado com aplicações do Serviço de Aplicações Azure, consulte o seguinte artigo sobre o registo de [domínios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Experimentando a Topologia Distribuída
O resultado final da configuração do Gestor de `www.scalableasedemo.com` Tráfego e dNS é que os pedidos irão fluir através da seguinte sequência:

1. Um navegador ou dispositivo fará uma procura de DNS`www.scalableasedemo.com`
2. A entrada cname no registo de domínio faz com que a procura de DNS seja redirecionada para o Gestor de Tráfego Azure.
3. Um look up DNS é feito para *scalable-ase-demo.trafficmanager.net* contra um dos servidores DNS do Gestor de Tráfego Azure.
4. Com base na política de equilíbrio de carga (o parâmetro *TrafficRoutingMethod* utilizado anteriormente ao criar o perfil do Gestor de Tráfego), o Traffic Manager selecionará um dos pontos finais configurados e devolverá o FQDN desse ponto final ao navegador ou dispositivo.
5. Uma vez que o FQDN do ponto final é o Url de uma instância de aplicação que funciona num Ambiente de Serviço de Aplicações, o navegador ou dispositivo pedirá a um servidor DNS microsoft Azure para resolver o FQDN num endereço IP. 
6. O navegador ou dispositivo enviará o pedido HTTP/S para o endereço IP.  
7. O pedido chegará a uma das instâncias da aplicação em execução num dos Ambientes de Serviço de Aplicações.

A imagem da consola abaixo mostra um olhar DNS para o domínio personalizado da aplicação da amostra resolvendo com sucesso uma instância de aplicação em execução em um dos três ambientes de serviço de aplicação de aplicação (neste caso o segundo dos três Ambientes de Serviço de Aplicações):

![DNS Lookup][DNSLookup] 

## <a name="additional-links-and-information"></a>Links e Informações Adicionais
Documentação sobre o suporte do [Powershell Azure Resource Manager Traffic Manager][ARMTrafficManager].  

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
