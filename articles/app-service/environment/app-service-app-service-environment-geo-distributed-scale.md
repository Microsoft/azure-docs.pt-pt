---
title: Escala distribuída por geo
description: Aprenda a escalar horizontalmente aplicações usando geo-distribuição com Gestor de Tráfego e Ambientes de Serviço de Aplicações.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions, devx-track-azurepowershell
ms.openlocfilehash: 215132888749a54996b3341e43ef8d91c101a460
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834305"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Escala Distribuída Geograficamente com Ambientes de Serviço de Aplicações
## <a name="overview"></a>Descrição Geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os cenários de aplicação que requerem uma escala muito elevada podem exceder a capacidade de recursos computacional disponíveis para uma única implementação de uma aplicação.  Aplicações de voto, eventos desportivos e eventos de entretenimento televisivo são todos exemplos de cenários que requerem uma escala extremamente elevada. Requisitos de alta escala podem ser cumpridos escalando horizontalmente aplicações. Para lidar com requisitos de carga extrema, muitas implementações de aplicações podem ser feitas numa única região e em regiões.

Os Ambientes de Serviço de Aplicações são uma plataforma ideal para a escala horizontal. Depois de selecionar uma configuração de Ambiente de Serviço de Aplicações que pode suportar uma taxa de pedido conhecida, os desenvolvedores podem implementar ambientes de serviço de aplicações adicionais em forma de "cortador de cookies" para obter uma capacidade de carga de pico desejada.

Por exemplo, suponha que uma aplicação em execução numa configuração do Ambiente do Serviço de Aplicações foi testada para lidar com pedidos de 20K por segundo (RPS).  Se a capacidade de carga máxima desejada for de 100K RPS, podem ser criados cinco (5) Ambientes de Serviço de Aplicações e configurados para garantir que a aplicação pode suportar a carga máxima projetada.

Uma vez que os clientes normalmente acedem a apps usando um domínio personalizado (ou vaidade), os desenvolvedores precisam de uma forma de distribuir pedidos de aplicações em todas as instâncias do App Service Environment.  Uma ótima maneira de alcançar este objetivo é resolver o domínio personalizado usando um [perfil de Gestor de Tráfego Azure][AzureTrafficManagerProfile].  O perfil do Gestor de Tráfego pode ser configurado para apontar em todos os Ambientes de Serviço de Aplicações individuais.  O Gestor de Tráfego tratará automaticamente a distribuição de clientes em todos os Ambientes de Serviço de Aplicações, com base nas definições de equilíbrio de carga no perfil de Gestor de Tráfego.  Esta abordagem funciona independentemente de todos os Ambientes de Serviço de Aplicações serem implantados numa única região de Azure, ou implantados em todo o mundo em várias regiões do Azure.

Além disso, uma vez que os clientes acedem a apps através do domínio da vaidade, os clientes desconhecem o número de Ambientes de Serviço de Aplicação que executam uma aplicação.  Como resultado, os desenvolvedores podem adicionar e remover, de forma rápida e fácil, ambientes de serviço de aplicações com base na carga de tráfego observada.

O diagrama conceptual abaixo retrata uma aplicação horizontalmente dimensionada em três Ambientes de Serviço de Aplicações dentro de uma única região.

![Arquitetura Conceptual][ConceptualArchitecture] 

O restante deste tópico percorre os passos envolvidos na criação de uma topologia distribuída para a aplicação da amostra utilizando vários Ambientes de Serviço de Aplicações.

## <a name="planning-the-topology"></a>Planejamento da Topologia
Antes de construir uma pegada de aplicação distribuída, ajuda a ter algumas informações de peças com antecedência.

* **Domínio personalizado para a aplicação:**  Qual é o nome de domínio personalizado que os clientes usarão para aceder à app?  Para a aplicação da amostra, o nome de domínio personalizado é `www.scalableasedemo.com` .
* **Domínio do Gestor de Tráfego:** Escolha um nome de domínio ao criar um [perfil de Gestor de Tráfego Azure][AzureTrafficManagerProfile].  Este nome será combinado com o *sufixo trafficmanager.net* para registar uma entrada de domínio que é gerida pelo Traffic Manager.  Para a aplicação da amostra, o nome escolhido é *escalável-ase-demo*.  Como resultado, o nome de domínio completo que é gerido pelo Traffic Manager é *scalable-ase-demo.trafficmanager.net*.
* **Estratégia para escalar a pegada da aplicação:**  A pegada da aplicação será distribuída por vários Ambientes de Serviço de Aplicações numa única região?  Várias regiões?  Uma mistura de ambas as abordagens?  Baseie a decisão nas expectativas de onde o tráfego de clientes será originário, e quão bem o resto de uma aplicação suporta infraestruturas back-end pode escalar.  Por exemplo, com uma aplicação 100% apátrida, uma aplicação pode ser massivamente dimensionada usando uma combinação de muitos Ambientes de Serviço de Aplicações em cada região de Azure, multiplicada por Ambientes de Serviço de Aplicações implantados em muitas regiões do Azure.  Com mais de 15 regiões globais de Azure disponíveis para escolher, os clientes podem realmente construir uma pegada de aplicação de hiperescala em todo o mundo.  Para a aplicação de amostras que é usada para este artigo, três Ambientes de Serviço de Aplicações foram criados numa única região de Azure (South Central US).
* **Convenção de nomeação para os Ambientes de Serviço de Aplicações:**  Cada Ambiente de Serviço de Aplicações requer um nome único.  Além de um ou dois Ambientes de Serviço de Aplicações, é útil ter uma convenção de nomeação para ajudar a identificar cada Ambiente de Serviço de Aplicações.  Para a aplicação da amostra, foi utilizada uma simples convenção de nomeação.  Os nomes dos três Ambientes de Serviço de Aplicações são *fe1ase,* *fe2ase* e *fe3ase.*
* **Convenção de nomeação para as aplicações:**  Uma vez que serão implementados vários casos da aplicação, é necessário um nome para cada instância da aplicação implementada.  Uma característica pouco conhecida mas conveniente de Ambientes de Serviço de Aplicações é que o mesmo nome de aplicação pode ser usado em vários Ambientes de Serviço de Aplicações.  Uma vez que cada App Service Environment tem um sufixo de domínio único, os desenvolvedores podem optar por reutilizar exatamente o mesmo nome de aplicação em cada ambiente.  Por exemplo, um programador poderia ter aplicações nomeadas da seguinte forma:  *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc.  No entanto, para a aplicação da amostra, cada instância de aplicação também tem um nome único.  Os nomes de instâncias de aplicações utilizados são *webfrontend1,* *webfrontend2* e *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configuração do Perfil do Gestor de Tráfego
Uma vez implementadas múltiplas instâncias de uma aplicação em vários Ambientes de Serviço de Aplicações, as instâncias individuais da aplicação podem ser registadas com o Traffic Manager.  Para a aplicação da amostra, é necessário um perfil de Gestor de Tráfego para *scalable-ase-demo.trafficmanager.net* que possa encaminhar os clientes para qualquer uma das seguintes instâncias de aplicações implementadas:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Um exemplo da aplicação da amostra implementada no primeiro Ambiente de Serviço de Aplicações.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Um exemplo da aplicação da amostra implementada no segundo Ambiente de Serviço de Aplicações.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Um caso da aplicação da amostra implantada no terceiro Ambiente de Serviço de Aplicações.

A forma mais fácil de registar vários pontos finais do Azure App Service, todos em execução na **mesma** região do Azure, é com o suporte do Gestor de Tráfego do PowerShell [Azure Resource Manager][ARMTrafficManager].  

O primeiro passo é criar um perfil de Gestor de Tráfego Azure.  O código abaixo mostra como o perfil foi criado para a aplicação da amostra:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Note como o parâmetro *RelativoDnsName* foi definido para *escalável-ase-demo*.  Este parâmetro faz com que o nome de domínio *scalable-ase-demo.trafficmanager.net* ser criado e associado a um perfil de Gestor de Tráfego.

O parâmetro *TrafficRoutingMethod* define a política de equilíbrio de carga que o Traffic Manager utilizará para determinar como espalhar a carga do cliente em todos os pontos finais disponíveis.  Neste exemplo, o método *ponderado* foi escolhido.  Devido a esta escolha, os pedidos do cliente serão distribuídos por todos os pontos finais de aplicação registados com base nos pesos relativos que estão associados a cada ponto final. 

Com o perfil criado, cada instância de aplicação é adicionada ao perfil como um ponto final nativo do Azure.  O código seguinte procura uma referência a cada aplicação web frontal. Em seguida, adiciona cada aplicação como um ponto final do Traffic Manager através do parâmetro *TargetResourceId.*

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Note como há uma chamada para *Add-AzureTrafficManagerEndpointConfig* para cada instância de aplicação individual.  O parâmetro *TargetResourceId* em cada comando PowerShell refere uma das três instâncias de aplicação implementadas.  O perfil do Gestor de Tráfego irá espalhar a carga pelos três pontos finais registados no perfil.

Todos os três pontos finais usam o mesmo valor (10) para o parâmetro *Peso.*  Esta situação resulta em Traffic Manager a espalhar os pedidos de clientes em todas as três instâncias de aplicações de forma relativamente uniforme. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Apontando o domínio personalizado da app no domínio do gestor de tráfego
O passo final necessário é apontar o domínio personalizado da app no domínio do Gestor de Tráfego.  Para a aplicação da amostra, `www.scalableasedemo.com` aponte para `scalable-ase-demo.trafficmanager.net` .  Complete este passo com o registrador de domínio que gere o domínio personalizado.  

Utilizando as ferramentas de gestão de domínio do seu registo, é necessário criar registos CNAME que apontam o domínio personalizado no domínio do Gestor de Tráfego.  A imagem abaixo mostra um exemplo de como esta configuração CNAME se parece:

![CNAME para domínio personalizado][CNAMEforCustomDomain] 

Apesar de não estar abordado neste tópico, lembre-se que cada instância de aplicação individual precisa de ter o domínio personalizado registado com ele também.  Caso contrário, se um pedido chegar a uma instância de aplicação, e a aplicação não tiver registado o domínio personalizado com a app, o pedido falhará.

Neste exemplo, o domínio personalizado é `www.scalableasedemo.com` , e cada instância de aplicação tem o domínio personalizado associado ao mesmo.

![Domínio Personalizado][CustomDomain] 

Para recapitular um domínio personalizado com aplicações do Azure App Service, consulte [registar domínios personalizados.][RegisterCustomDomain]

## <a name="trying-out-the-distributed-topology"></a>Experimentando a Topologia Distribuída
O resultado final da configuração do Gestor de Tráfego e do DNS é que os pedidos de `www.scalableasedemo.com` fluxo através da seguinte sequência:

1. Um navegador ou dispositivo fará uma procura de DNS para `www.scalableasedemo.com`
2. A entrada CNAME no registo de domínio faz com que o lookup DNS seja redirecionado para Azure Traffic Manager.
3. É feita uma procura de DNS para *scalable-ase-demo.trafficmanager.net* contra um dos servidores DNS do Azure Traffic Manager.
4. Com base na política de equilíbrio de carga especificada anteriormente no parâmetro *TrafficRoutingMethod,* o Gestor de Tráfego seleciona um dos pontos finais configurados. Em seguida, devolve o FQDN desse ponto final ao navegador ou dispositivo.
5. Uma vez que o FQDN do ponto final é o Url de uma instância de aplicação que funciona num Ambiente de Serviço de Aplicações, o navegador ou dispositivo irá pedir a um servidor DNS da Microsoft Azure para resolver o FQDN para um endereço IP. 
6. O navegador ou dispositivo enviará o pedido HTTP/S para o endereço IP.  
7. O pedido chegará a uma das instâncias de aplicação em execução num dos Ambientes de Serviço de Aplicações.

A imagem da consola abaixo mostra uma pesquisa de DNS para o domínio personalizado da aplicação da amostra. Resolve com sucesso uma aplicação que funciona num dos três ambientes de serviço de aplicações da amostra (neste caso, o segundo dos três Ambientes de Serviço de Aplicações):

![DNS Lookup][DNSLookup] 

## <a name="additional-links-and-information"></a>Links e Informações Adicionais
Documentação sobre o suporte do Gestor [de Tráfego powerShell Azure Resource Manager][ARMTrafficManager].  

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
