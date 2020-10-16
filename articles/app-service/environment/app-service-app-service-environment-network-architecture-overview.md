---
title: Arquitetura de rede v1
description: Visão geral arquitetônica da topologia da rede de Ambientes de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84701818"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Visão geral da arquitetura da rede dos ambientes de serviço de aplicações
Os Ambientes de Serviço de Aplicações são sempre criados dentro de uma sub-rede de uma [rede virtual][virtualnetwork] - as aplicações em execução num Ambiente de Serviço de Aplicações podem comunicar com pontos finais privados localizados dentro da mesma topologia de rede virtual.  Uma vez que os clientes podem bloquear partes da sua infraestrutura de rede virtual, é importante compreender os tipos de fluxos de comunicação de rede que ocorrem com um Ambiente de Serviço de Aplicações.

## <a name="general-network-flow"></a>Fluxo geral de rede
Quando um Ambiente de Serviço de Aplicações (ASE) utiliza um endereço IP virtual público (VIP) para aplicações, todo o tráfego de entrada chega a esse VIP público.  Isto inclui tráfego HTTP e HTTPS para apps, bem como outro tráfego para FTP, funcionalidade de depuração remota e operações de gestão Azure.  Para uma lista completa das portas específicas (necessárias e opcionais) que estão disponíveis no VIP público consulte o artigo sobre o controlo do [tráfego de entrada][controllinginboundtraffic] para um Ambiente de Serviço de Aplicações. 

Os Ambientes de Serviço de Aplicações também suportam aplicações de execução que estão ligadas apenas a um endereço interno de rede virtual, também referido como um endereço ILB (balanceador de carga interna).  Num tráfego de ILB, HTTP e HTTPS para apps, bem como chamadas de depurações remotas, chegam ao endereço ILB.  Para as configurações ILB-ASE mais comuns, o tráfego FTP/FTPS também chegará no endereço ILB.  No entanto, as operações de gestão da Azure continuarão a fluir para os portos 454/455 no VIP público de um ILB habilitado para a ASE.

O diagrama abaixo mostra uma visão geral dos vários fluxos de rede de entrada e saída para um Ambiente de Serviço de Aplicações onde as aplicações estão ligadas a um endereço IP virtual público:

![Fluxos gerais de rede][GeneralNetworkFlows]

Um Ambiente de Serviço de Aplicações pode comunicar com uma variedade de pontos finais de clientes privados.  Por exemplo, as aplicações em execução no Ambiente de Serviço de Aplicações podem ligar-se ao servidor de base de dados(s) em execução em máquinas virtuais IaaS na mesma topologia de rede virtual.

> [!IMPORTANT]
> Olhando para o diagrama da rede, os "Outros Recursos Compute" são implantados numa sub-rede diferente do Ambiente de Serviço de Aplicações. A implantação de recursos na mesma Sub-rede com a ASE bloqueará a conectividade da ASE para esses recursos (exceto para o encaminhamento intra-ASE específico). Em vez disso, inserte-se numa sub-rede diferente (no mesmo VNET). O Ambiente de Serviço de Aplicações será então capaz de se conectar. Não é necessária nenhuma configuração adicional.
> 
> 

Os Ambientes de Serviço de Aplicações também comunicam com os recursos de armazenamento sql DB e Azure necessários para gerir e operar um Ambiente de Serviço de Aplicações.  Alguns dos recursos sql e armazenamento com que um Ambiente de Serviço de Aplicações comunica estão localizados na mesma região que o Ambiente de Serviço de Aplicações, enquanto outros estão localizados em regiões remotas de Azure.  Como resultado, a conectividade de saída para a Internet é sempre necessária para que um Ambiente de Serviço de Aplicações funcione corretamente. 

Uma vez que um Ambiente de Serviço de Aplicações é implantado numa sub-rede, os grupos de segurança da rede podem ser usados para controlar o tráfego de entrada na sub-rede.  Para obter mais informações sobre como controlar o tráfego de entrada num Ambiente de Serviço de Aplicações, consulte o seguinte [artigo][controllinginboundtraffic].

Para obter mais informações sobre como permitir a conectividade de saída da Internet a partir de um Ambiente de Serviço de Aplicações, consulte o seguinte artigo sobre como trabalhar com a [Via Expresso.][ExpressRoute]  A mesma abordagem descrita no artigo aplica-se ao trabalhar com a conectividade site-to-site e utilizando túneis forçados.

## <a name="outbound-network-addresses"></a>Endereços de rede de saída
Quando um Ambiente de Serviço de Aplicações faz chamadas de saída, um endereço IP está sempre associado às chamadas de saída.  O endereço IP específico que é utilizado depende se o ponto final a ser chamado está localizado dentro da topologia da rede virtual, ou fora da topologia da rede virtual.

Se o ponto final que está a ser chamado está **fora** da topologia da rede virtual, então o endereço de saída (também conhecido como o endereço NAT de saída) que é utilizado é o VIP público do Ambiente de Serviço de Aplicações.  Este endereço pode ser encontrado na interface do utilizador do portal para o Ambiente de Serviço de Aplicações em Propriedades.

![Endereço IP de saída][OutboundIPAddress]

Este endereço também pode ser determinado para ASEs que apenas têm um VIP público, criando uma app no Ambiente de Serviço de Aplicações, e, em seguida, executando um *nslookup* no endereço da app. O endereço IP resultante é tanto o VIP público, como o endereço NAT de saída do App Service Environment.

Se o ponto final que está a ser chamado estiver **dentro** da topologia da rede virtual, o endereço de saída da aplicação de chamadas será o endereço IP interno do recurso compute individual que executa a app.  No entanto, não existe um mapeamento persistente de endereços IP internos da rede virtual para apps.  As aplicações podem mover-se através de diferentes recursos computacional, e o conjunto de recursos de computação disponíveis num Ambiente de Serviço de Aplicações pode mudar devido a operações de escala.

No entanto, uma vez que um Ambiente de Serviço de Aplicações está sempre localizado dentro de uma sub-rede, é garantido que o endereço IP interno de um recurso compute que executa uma aplicação estará sempre dentro da gama CIDR da sub-rede.  Como resultado, quando acLs de grãos finos ou grupos de segurança de rede são usados para garantir o acesso a outros pontos finais dentro da rede virtual, a gama de sub-redes que contém o App Service Environment precisa de ter acesso.

O seguinte diagrama mostra estes conceitos com mais detalhe:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

* Uma vez que o VIP público do Ambiente de Serviço de Aplicações é 192.23.1.2, este é o endereço IP de saída utilizado ao fazer chamadas para pontos finais "Internet".
* A gama CIDR da sub-rede contendo o Ambiente de Serviço de Aplicações é de 10.0.1.0/26.  Outros pontos finais dentro da mesma infraestrutura de rede virtual verão chamadas de apps como originárias de algum lugar dentro deste intervalo de endereços.

## <a name="calls-between-app-service-environments"></a>Chamadas entre ambientes de serviço de aplicações
Um cenário mais complexo pode ocorrer se implementar vários Ambientes de Serviço de Aplicações na mesma rede virtual, e fazer chamadas de saída de um Ambiente de Serviço de Aplicações para outro Ambiente de Serviço de Aplicações.  Este tipo de chamadas de Ambiente de Serviço de Aplicações cruzadas também serão tratados como chamadas "Internet".

O diagrama que se segue mostra um exemplo de uma arquitetura em camadas com apps num Ambiente de Serviço de Aplicações (por exemplo, aplicações web "Porta da Frente") que ligam para apps num segundo Ambiente de Serviço de Aplicações (por exemplo, aplicações internas de API não destinadas a serem acessíveis a partir da Internet). 

![Chamadas entre ambientes de serviço de aplicações][CallsBetweenAppServiceEnvironments] 

No exemplo acima do Ambiente de Serviço de Aplicações "ASE One" tem um endereço IP de saída de 192.23.1.2.  Se uma aplicação em execução neste App Service Environment fizer uma chamada de saída para uma aplicação em execução num segundo App Service Environment ("ASE Two") localizada na mesma rede virtual, a chamada de saída será tratada como uma chamada de "Internet".  Como resultado, o tráfego de rede que chega ao segundo Ambiente de Serviço de Aplicações apresentará como originário de 192.23.1.2 (isto é, não a gama de endereços de sub-rede do primeiro Ambiente de Serviço de Aplicações).

Apesar de as chamadas entre diferentes Ambientes de Serviço de Aplicações serem tratadas como chamadas "Internet", quando ambos os Ambientes de Serviço de Aplicações estão localizados na mesma região do Azure, o tráfego de rede permanecerá na rede regional Azure e não fluirá fisicamente através da Internet pública.  Como resultado, pode utilizar um grupo de segurança de rede na sub-rede do segundo App Service Environment para permitir apenas chamadas de entrada a partir do primeiro Ambiente de Serviço de Aplicações (cujo endereço IP de saída é 192.23.1.2), garantindo assim uma comunicação segura entre os Ambientes de Serviço de Aplicação.

## <a name="additional-links-and-information"></a>Links e Informações Adicionais
Detalhes sobre portas de entrada utilizadas pelos Ambientes de Serviço de Aplicações e a utilização de grupos de segurança de rede para controlar o tráfego de entrada está disponível [aqui.][controllinginboundtraffic]

Neste [artigo,][ExpressRoute]estão disponíveis detalhes sobre a utilização de rotas definidas pelo utilizador para garantir o acesso à Internet de saída a Ambientes de Serviços de Aplicação. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

