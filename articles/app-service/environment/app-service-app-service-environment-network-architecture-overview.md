---
title: Arquitetura de rede v1
description: Visão geral arquitetónica da topologia da rede de ambientes de serviço de aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374191"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Visão geral da arquitetura de rede dos ambientes de serviço de aplicações
Os Ambientes de Serviço de Aplicações são sempre criados dentro de uma subnet de uma [rede virtual][virtualnetwork] - as aplicações que executam um App Service Environment podem comunicar com pontos finais privados localizados dentro da mesma topologia de rede virtual.  Uma vez que os clientes podem bloquear partes da sua infraestrutura de rede virtual, é importante compreender os tipos de fluxos de comunicação de rede que ocorrem com um Ambiente de Serviço de Aplicações.

## <a name="general-network-flow"></a>Fluxo geral da rede
Quando um App Service Environment (ASE) usa um endereço IP virtual público (VIP) para aplicações, todo o tráfego de entrada chega a esse VIP público.  Isto inclui tráfego HTTP e HTTPS para apps, bem como outros tráfego saem para FTP, funcionalidade de depuração remota e operações de gestão azure.  Para obter uma lista completa das portas específicas (necessárias e opcionais) disponíveis no VIP público, consulte o artigo sobre o controlo do [tráfego de entrada][controllinginboundtraffic] para um Ambiente de Serviço de Aplicações. 

Os Ambientes de Serviço de Aplicações também suportam aplicações de execução que estão ligadas apenas a um endereço interno de rede virtual, também referido como um endereço ILB (equilibrador de carga interna).  Num iLB habilitado para o tráfego ASE, HTTP e HTTPS para apps, bem como chamadas de depuração remota, chegar no endereço ILB.  Para as configurações iLB-ASE mais comuns, o tráfego FTP/FTPS também chegará no endereço ILB.  No entanto, as operações de gestão da Azure continuarão a fluir para os portos 454/455 no VIP público de um ILB habilitado a ASE.

O diagrama abaixo mostra uma visão geral dos vários fluxos de rede de entrada e saída para um Ambiente de Serviço de Aplicações onde as aplicações estão ligadas a um endereço IP virtual público:

![Fluxos gerais de rede][GeneralNetworkFlows]

Um Ambiente de Serviço de Aplicações pode comunicar com uma variedade de pontos finais de clientes privados.  Por exemplo, as aplicações que executam o App Service Environment podem ligar-se a servidores de base de dados que executam em máquinas virtuais IaaS na mesma topologia de rede virtual.

> [!IMPORTANT]
> Olhando para o diagrama de rede, os "Outros Recursos Computacionais" são implantados numa subnet diferente do Ambiente de Serviço de Aplicações. A implantação de recursos na mesma Subnet com a ASE bloqueará a conectividade da ASE para esses recursos (com exceção do encaminhamento intra-ASE específico). Desloque-se a uma subnet diferente (no mesmo VNET). O Ambiente do Serviço de Aplicações será então capaz de se conectar. Não é necessária nenhuma configuração adicional.
> 
> 

Os Ambientes de Serviço de Aplicações também comunicam com recursos de Armazenamento Sql DB e Azure necessários para gerir e operar um Ambiente de Serviço de Aplicações.  Alguns dos recursos Sql e Storage com que um App Service Environment comunica estão localizados na mesma região que o App Service Environment, enquanto outros estão localizados em regiões remotas de Azure.  Como resultado, a conectividade de saída para a Internet é sempre necessária para que um Ambiente de Serviço de Aplicações funcione corretamente. 

Uma vez que um Ambiente de Serviço de Aplicações é implantado numa subnet, os grupos de segurança da rede podem ser usados para controlar o tráfego de entrada na subrede.  Para obter mais informações sobre como controlar o tráfego de entrada para um Ambiente de Serviço de Aplicações, consulte o [seguinte artigo][controllinginboundtraffic].

Para mais detalhes sobre como permitir a conectividade de saída da Internet a partir de um Ambiente de Serviço de Aplicações, consulte o seguinte artigo sobre trabalhar com a [Rota Expressa][ExpressRoute].  A mesma abordagem descrita no artigo aplica-se quando se trabalha com conectividade Local-a-Local e utiliza túneis forçados.

## <a name="outbound-network-addresses"></a>Endereços de rede de saída
Quando um Ambiente de Serviço de Aplicações faz chamadas de saída, um Endereço IP está sempre associado às chamadas de saída.  O endereço IP específico que é usado depende se o ponto final chamado está localizado dentro da topologia da rede virtual, ou fora da topologia da rede virtual.

Se o ponto final que está a ser chamado **fora** da topologia da rede virtual, então o endereço de saída (também conhecido como o endereço NAT de saída) que é usado é o VIP público do Ambiente de Serviço de Aplicações.  Este endereço pode ser encontrado na interface de utilizador do portal para o Ambiente do Serviço de Aplicações em Propriedades.

![Endereço IP de saída][OutboundIPAddress]

Este endereço também pode ser determinado para ASEs que apenas têm um VIP público através da criação de uma app no App Service Environment, e, em seguida, realizando uma *olhada* no endereço da app. O endereço IP resultante é tanto o VIP público, como o endereço NAT de saída do App Service Environment.

Se o ponto final ser chamado estiver **dentro** da topologia da rede virtual, o endereço de saída da aplicação de chamada será o endereço IP interno do recurso computacional individual que executa a app.  No entanto, não existe um mapeamento persistente de endereços IP internos da rede virtual para apps.  As aplicações podem mover-se através de diferentes recursos computacionais, e o conjunto de recursos computacionais disponíveis em um App Service Environment pode mudar devido a operações de escala.

No entanto, uma vez que um Ambiente de Serviço de Aplicação está sempre localizado dentro de uma subnet, é garantido que o endereço IP interno de um recurso computacional que executa uma aplicação estará sempre dentro da gama CIDR da subnet.  Como resultado, quando os ACLs de grão fino ou grupos de segurança de rede são utilizados para garantir o acesso a outros pontos finais dentro da rede virtual, a gama de subredes que contém o Ambiente de Serviço seletiva precisa de ter acesso.

O diagrama seguinte mostra estes conceitos com mais detalhes:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

* Uma vez que o VIP público do App Service Environment é 192.23.1.2, este é o endereço IP de saída usado para fazer chamadas para pontos finais "Internet".
* A gama CIDR da sub-rede contendo para o Ambiente de Serviço de Aplicações é de 10.0.1.0/26.  Outros pontos finais dentro da mesma infraestrutura de rede virtual verão chamadas de apps como originárias de algum lugar dentro desta gama de endereços.

## <a name="calls-between-app-service-environments"></a>Chamadas entre ambientes de serviço de aplicações
Um cenário mais complexo pode ocorrer se implementar vários Ambientes de Serviços de Aplicações na mesma rede virtual, e fazer chamadas de saída de um Ambiente de Serviço de Aplicação para outro Ambiente de Serviço de Aplicações.  Este tipo de chamadas de serviço de aplicações cruzadas O ambiente também será tratado como chamadas "Internet".

O diagrama seguinte mostra um exemplo de uma arquitetura em camadas com aplicações em um App Service Environment (por exemplo, aplicações web "Porta frontal" ) chamando aplicações para um segundo App Service Environment (por exemplo, aplicações internas de API de back-end que não pretendem ser acessíveis a partir da Internet). 

![Chamadas entre ambientes de serviço de aplicações][CallsBetweenAppServiceEnvironments] 

No exemplo acima do Ambiente de Serviço de Aplicações "ASE One" tem um endereço IP de saída de 192.23.1.2.  Se uma aplicação em execução nesta App Service Environment fizer uma chamada de saída para uma aplicação que funciona numa segunda App Service Environment ("ASE Two") localizada na mesma rede virtual, a chamada de saída será tratada como uma chamada de "Internet".  Como resultado, o tráfego de rede que chega no segundo App Service Environment mostrará como originário de 192.23.1.2 (ou seja, não a gama de endereços de sub-rede do primeiro Ambiente de Serviço de Aplicações).

Apesar de as chamadas entre diferentes ambientes de serviços de aplicações serem tratadas como chamadas de "Internet", quando ambos os Ambientes de Serviço sinuoso estão localizados na mesma região do Azure, o tráfego de rede permanecerá na rede regional de Azure e não fluirá fisicamente sobre a Internet pública.  Como resultado, você pode usar um grupo de segurança de rede na subnet do segundo App Service Environment para apenas permitir chamadas de entrada a partir do primeiro App Service Environment (cujo endereço IP de saída é 192.23.1.2), garantindo assim uma comunicação segura entre a App Ambientes de Serviço.

## <a name="additional-links-and-information"></a>Links e Informações Adicionais
Estão disponíveis detalhes sobre as portas de entrada utilizadas pelos Ambientes de Serviço de Aplicações e utilizando grupos de segurança de rede para controlar o tráfego de entrada [.][controllinginboundtraffic]

Neste [artigo][ExpressRoute]estão disponíveis detalhes sobre a utilização de rotas definidas pelo utilizador para conceder acesso à Internet de saída aos Ambientes de Serviço sinuoso. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

