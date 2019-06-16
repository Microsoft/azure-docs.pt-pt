---
title: Descrição geral da arquitetura de rede de ambientes de serviço de aplicações - Azure
description: Descrição geral da arquitetura de topologia de rede ofApp ambientes de serviço.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 0d7d4af46e54ad89e0d084cb15af13e56115e996
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60765316"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Descrição Geral da Arquitetura de Rede de Ambientes de Serviço de Aplicações
## <a name="introduction"></a>Introdução
Ambientes de serviço de aplicações são sempre criados dentro de uma sub-rede de uma [rede virtual] [ virtualnetwork] -as aplicações em execução num ambiente de serviço de aplicações podem comunicar com pontos finais privados localizados dentro da mesma virtual topologia de rede.  Uma vez que os clientes podem bloquear partes de sua infra-estrutura de rede virtual, é importante compreender os tipos de fluxos de comunicação de rede que ocorrem com um ambiente de serviço de aplicações.

## <a name="general-network-flow"></a>Fluxo de rede geral
Quando um ambiente de serviço de aplicações (ASE) utiliza um endereço IP virtual público (VIP) para aplicações, todo o tráfego de entrada chega a esse VIP público.  Isto inclui o tráfego HTTP e HTTPS para aplicações, bem como outros tráfegos de FTP, a funcionalidade de depuração remota e a operações de gestão do Azure.  Para obter uma lista completa das portas específicas (obrigatórios e opcionais) que estão disponíveis no VIP público, consulte o artigo sobre [controlar o tráfego de entrada] [ controllinginboundtraffic] para um ambiente de serviço de aplicações. 

Ambientes de serviço de aplicações também suportam a execução das aplicações associadas apenas a um endereço interno de rede virtual, também conhecido como um endereço do ILB (Balanceador de carga interno).  Num ILB ativado tráfego ASE, HTTP e HTTPS para aplicações, bem como as chamadas remotas de depuração, chegam ao endereço do ILB.  Para configurações mais comuns do ASE de ILB, tráfego FTP/FTPS também chegarão no endereço de ILB.  No entanto a operações de gestão do Azure ainda vão fluir para portas 454/455 o VIP pública de um ILB ativado ASE.

O diagrama abaixo mostra uma descrição geral dos vários fluxos de rede de entrada e saída para um ambiente de serviço de aplicações em que as aplicações associadas a um endereço IP virtual público:

![Fluxos de rede geral][GeneralNetworkFlows]

Um ambiente de serviço de aplicações podem se comunicar com uma variedade de pontos de extremidade privados de clientes.  Por exemplo, as aplicações em execução no ambiente do serviço de aplicações podem ligar ao servidor (es) da base de dados em execução em máquinas de virtuais de IaaS na mesma topologia de rede virtual.

> [!IMPORTANT]
> Analisar o diagrama de rede, o "outras computação recursos" são implementados numa sub-rede diferente do ambiente de serviço de aplicações. Implementar recursos na mesma sub-rede com o ASE irá bloquear a conectividade entre o ASE e esses recursos (exceto para o encaminhamento de intra-ASE específico). Implemente a uma sub-rede diferente em vez disso (na mesma VNET). O ambiente de serviço de aplicações, em seguida, será possível estabelecer ligação. É necessária nenhuma configuração adicional.
> 
> 

Ambientes de serviço de aplicações também se comunicar com BD Sql e armazenamento do Azure recursos necessários para gerenciar e operar um ambiente de serviço de aplicações.  Alguns dos recursos do Sql e armazenamento que um ambiente de serviço de aplicação comunica com estão localizados na mesma região que o ambiente de serviço de aplicações, enquanto outros estão localizados em regiões do Azure remotos.  Como resultado, a conectividade de saída à Internet sempre é necessária para um ambiente de serviço de aplicações funcionar corretamente. 

Uma vez que um ambiente de serviço de aplicações é implementado numa sub-rede, grupos de segurança de rede podem ser utilizados para controlar o tráfego de entrada para a sub-rede.  Para obter detalhes sobre como controlar o tráfego de entrada para um ambiente de serviço de aplicações, consulte o seguinte procedimento [artigo][controllinginboundtraffic].

Para obter detalhes sobre como permitir que a conectividade de Internet de saída de um ambiente de serviço de aplicações, consulte o seguinte artigo sobre como trabalhar com [Express Route][ExpressRoute].  A mesma abordagem descrita no artigo aplica-se ao trabalhar com a conectividade Site a Site e utilizar o túnel forçado.

## <a name="outbound-network-addresses"></a>Endereços de rede de saída
Quando um ambiente de serviço de aplicações faz chamadas de saída, um endereço IP é sempre associado com as chamadas de saída.  O endereço IP específico utilizado depende se o ponto final a ser chamado está localizado na topologia de rede virtual ou fora da topologia da rede virtual.

Se o ponto final a ser chamado está **fora** da topologia da rede virtual, em seguida, o endereço de saída (também conhecido como o endereço NAT saído) que é utilizado é o VIP público de ambiente de serviço de aplicações.  Este endereço pode ser encontrado na interface de utilizador do portal para o ambiente de serviço de aplicações no painel de propriedades.

![Endereço IP de saída][OutboundIPAddress]

Este endereço também pode ser determinado para os ASEs que têm apenas um VIP público ao criar uma aplicação no ambiente do serviço de aplicações e, em seguida, efetuar uma *nslookup* no endereço da aplicação. O endereço IP resultante é tanto o VIP público, bem como endereço NAT de saída do ambiente de serviço de aplicações.

Se o ponto final a ser chamado está **dentro de** da topologia da rede virtual, o endereço de saída da aplicação de chamada será o endereço IP interno do recurso de computação individuais que executa a aplicação.  No entanto não existe um mapeamento persistente de endereços IP internos de rede virtual para aplicações.  Aplicações podem ser movido entre em diversos recursos de computação e o conjunto de recursos num ambiente de serviço de aplicações podem alterar devido a operações de dimensionamento de computação disponível.

No entanto, uma vez que um ambiente de serviço de aplicações sempre está localizado numa sub-rede, terá a garantia de que o endereço IP interno de um recurso de computação em execução um aplicativo sempre se situam dentro do intervalo CIDR da sub-rede.  Como resultado, quando ACLs refinadas ou grupos de segurança de rede são utilizados para proteger o acesso aos outros pontos finais dentro da rede virtual, o intervalo de sub-rede que contém o ambiente de serviço de aplicações tem de ser concedido acesso.

O diagrama seguinte mostra esses conceitos mais detalhadamente:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

* Uma vez que o VIP público de ambiente de serviço de aplicações é 192.23.1.2, que é o endereço IP de saída utilizado ao efetuar chamadas para pontos finais "Internet".
* O intervalo CIDR da sub-rede que contém para o ambiente de serviço de aplicações é 10.0.1.0/26.  Outros pontos finais dentro da mesma infraestrutura de rede virtual irão ver chamadas a partir de aplicações como sendo proveniente em algum lugar dentro deste intervalo de endereço.

## <a name="calls-between-app-service-environments"></a>Chamadas entre os ambientes de serviço de aplicações
Um cenário mais complexo pode ocorrer se implementar vários ambientes de serviço de aplicações na mesma rede virtual e fazer chamadas de saída de um ambiente de serviço de aplicações para outro ambiente de serviço de aplicações.  Esses tipos de em várias chamadas também serão tratadas como chamadas de "Internet" ambiente de serviço de aplicações.

O diagrama seguinte mostra um exemplo de uma arquitetura em camadas com as aplicações num ambiente de serviço de aplicações (por exemplo "Porta de entrada" de aplicações web) chamar aplicações num ambiente de serviço de aplicações segundo (por exemplo, internas aplicações de API de back-end não se destina a ser acessível a partir da Internet). 

![Chamadas entre os ambientes de serviço de aplicações][CallsBetweenAppServiceEnvironments] 

No exemplo acima, o ambiente de serviço de aplicações "ASE um" tem um endereço IP de saída de 192.23.1.2.  Se uma aplicação em execução no ambiente de serviço de aplicações faz uma chamada de saída para uma aplicação em execução num ambiente de serviço de aplicações segundo ("ASE duas"), localizada na mesma rede virtual, a chamada de saída será tratado como uma chamada de "Internet".  Como resultado o tráfego de rede que chegam no segundo o ambiente de serviço de aplicações mostrará como sendo proveniente 192.23.1.2 (ou seja, não sub-rede intervalo de endereços da primeira App Service Environment).

Muito embora chamadas entre diferentes ambientes do serviço de aplicações são tratadas como chamadas de "Internet", quando ambos os ambientes do serviço de aplicações estão localizadas na mesma região do Azure o tráfego de rede irá permanecer na rede regional do Azure e não fisicamente fluxo sobre o Internet pública.  Como resultado pode utilizar um grupo de segurança de rede na sub-rede do ambiente de serviço de aplicações segundo para permitir apenas chamadas de entrada da primeira aplicação ambiente de serviço (cujo endereço IP de saída é 192.23.1.2), que garante uma comunicação segura entre a aplicação Ambientes de serviço.

## <a name="additional-links-and-information"></a>Links adicionais e informações
Obter detalhes sobre as portas utilizadas por ambientes do serviço de aplicações de entrada e utilizar grupos de segurança de rede para controlar o tráfego de entrada está disponível [aqui][controllinginboundtraffic].

Detalhes sobre como utilizar o utilizador definidas rotas para conceder acesso de saída à Internet para ambientes de serviço de aplicações está disponível neste [artigo][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

