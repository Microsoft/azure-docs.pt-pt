---
title: Visão geral da arquitetura de rede dos ambientes do serviço de aplicativo-Azure
description: Visão geral da arquitetura de ambientes de serviço ofApp de topologia de rede.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 98eb4d7440126bedb3d2e1de5711141eaac8b07a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070073"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Descrição Geral da Arquitetura de Rede de Ambientes de Serviço de Aplicações
## <a name="introduction"></a>Introdução
Os ambientes do serviço de aplicativo são sempre criados dentro de uma sub-rede de uma [rede virtual][virtualnetwork] -os aplicativos em execução em um ambiente do serviço de aplicativo podem se comunicar com pontos de extremidade privados localizados na mesma topologia de rede virtual.  Como os clientes podem bloquear partes de sua infraestrutura de rede virtual, é importante entender os tipos de fluxos de comunicação de rede que ocorrem com um Ambiente do Serviço de Aplicativo.

## <a name="general-network-flow"></a>Fluxo de rede geral
Quando um Ambiente do Serviço de Aplicativo (ASE) usa um endereço IP virtual público (VIP) para aplicativos, todo o tráfego de entrada chega nesse VIP público.  Isso inclui o tráfego HTTP e HTTPS para aplicativos, bem como outro tráfego para FTP, funcionalidade de depuração remota e operações de gerenciamento do Azure.  Para obter uma lista completa das portas específicas (obrigatórias e opcionais) que estão disponíveis no VIP público, consulte o artigo sobre como [controlar o tráfego de entrada][controllinginboundtraffic] para um ambiente do serviço de aplicativo. 

Ambientes de serviço de aplicativo também oferecem suporte a aplicativos em execução que são associados apenas a um endereço interno de rede virtual, também conhecido como endereço de ILB (balanceador de carga interno).  Em um ASE habilitado para ILB, o tráfego HTTP e HTTPS para aplicativos, bem como chamadas de depuração remota, chegam no endereço ILB.  Para as configurações mais comuns de ILB, o tráfego de FTP/FTPS também chegará no endereço ILB.  No entanto, as operações de gerenciamento do Azure ainda fluirão para as portas 454/455 no VIP público de um ASE habilitado para ILB.

O diagrama a seguir mostra uma visão geral dos vários fluxos de rede de entrada e saída para um Ambiente do Serviço de Aplicativo em que os aplicativos estão associados a um endereço IP virtual público:

![Fluxos de rede gerais][GeneralNetworkFlows]

Um Ambiente do Serviço de Aplicativo pode se comunicar com uma variedade de pontos de extremidade de clientes privados.  Por exemplo, os aplicativos em execução no Ambiente do Serviço de Aplicativo podem se conectar a servidores de banco de dados em execução em máquinas virtuais IaaS na mesma topologia de rede virtual.

> [!IMPORTANT]
> Observando o diagrama de rede, os "outros recursos de computação" são implantados em uma sub-rede diferente da Ambiente do Serviço de Aplicativo. A implantação de recursos na mesma sub-rede com o ASE bloqueará a conectividade do ASE para esses recursos (exceto para o roteamento específico do ASE). Em vez disso, implante em uma sub-rede diferente (na mesma VNET). O Ambiente do Serviço de Aplicativo será capaz de se conectar. Nenhuma configuração adicional é necessária.
> 
> 

Os ambientes do serviço de aplicativo também se comunicam com os recursos de armazenamento do Azure e do BD SQL necessários para gerenciar e operar um Ambiente do Serviço de Aplicativo.  Alguns dos recursos de armazenamento e SQL com os quais um Ambiente do Serviço de Aplicativo se comunica estão localizados na mesma região que o Ambiente do Serviço de Aplicativo, enquanto outros estão localizados em regiões remotas do Azure.  Como resultado, a conectividade de saída com a Internet é sempre necessária para que um Ambiente do Serviço de Aplicativo funcione corretamente. 

Como um Ambiente do Serviço de Aplicativo é implantado em uma sub-rede, os grupos de segurança de rede podem ser usados para controlar o tráfego de entrada para a sub-rede.  Para obter detalhes sobre como controlar o tráfego de entrada para um Ambiente do Serviço de Aplicativo, consulte o [artigo][controllinginboundtraffic]a seguir.

Para obter detalhes sobre como permitir a conectividade de saída da Internet de um Ambiente do Serviço de Aplicativo, consulte o artigo a seguir sobre como trabalhar com o [Express Route][ExpressRoute].  A mesma abordagem descrita no artigo se aplica ao trabalhar com conectividade site a site e usar o túnel forçado.

## <a name="outbound-network-addresses"></a>Endereços de rede de saída
Quando um Ambiente do Serviço de Aplicativo faz chamadas de saída, um endereço IP é sempre associado às chamadas de saída.  O endereço IP específico que é usado depende de se o ponto de extremidade que está sendo chamado está localizado dentro da topologia de rede virtual ou fora da topologia de rede virtual.

Se o ponto de extremidade que está sendo chamado estiver **fora** da topologia de rede virtual, o endereço de saída (também conhecido como endereço NAT de saída) que é usado é o VIP público do ambiente do serviço de aplicativo.  Esse endereço pode ser encontrado na interface do usuário do portal para o Ambiente do Serviço de Aplicativo na folha de propriedades.

![Endereço IP de saída][OutboundIPAddress]

Esse endereço também pode ser determinado para ASEs que têm apenas um VIP público criando um aplicativo no Ambiente do Serviço de Aplicativo e, em seguida, executando um *nslookup* no endereço do aplicativo. O endereço IP resultante é o VIP público, bem como o endereço NAT de saída do Ambiente do Serviço de Aplicativo.

Se o ponto de extremidade que está sendo chamado estiver **dentro** da topologia de rede virtual, o endereço de saída do aplicativo de chamada será o endereço IP interno do recurso de computação individual que executa o aplicativo.  No entanto, não há um mapeamento persistente de endereços IP internos da rede virtual para aplicativos.  Os aplicativos podem se movimentar entre diferentes recursos de computação e o pool de recursos de computação disponíveis em um Ambiente do Serviço de Aplicativo pode ser alterado devido a operações de dimensionamento.

No entanto, como um Ambiente do Serviço de Aplicativo sempre está localizado em uma sub-rede, você tem a garantia de que o endereço IP interno de um recurso de computação que executa um aplicativo sempre estará dentro do intervalo CIDR da sub-rede.  Como resultado, quando ACLs refinadas ou grupos de segurança de rede são usados para proteger o acesso a outros pontos de extremidade na rede virtual, o intervalo de sub-rede que contém o Ambiente do Serviço de Aplicativo precisa ter acesso concedido.

O diagrama a seguir mostra esses conceitos com mais detalhes:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

* Como o VIP público do Ambiente do Serviço de Aplicativo é 192.23.1.2, esse é o endereço IP de saída usado ao fazer chamadas para pontos de extremidade de "Internet".
* O intervalo CIDR da sub-rede que a contém para o Ambiente do Serviço de Aplicativo é 10.0.1.0/26.  Outros pontos de extremidade dentro da mesma infraestrutura de rede virtual verão chamadas de aplicativos como provenientes de algum lugar dentro deste intervalo de endereços.

## <a name="calls-between-app-service-environments"></a>Chamadas entre ambientes do serviço de aplicativo
Um cenário mais complexo pode ocorrer se você implantar vários ambientes de serviço de aplicativo na mesma rede virtual e fizer chamadas de saída de um Ambiente do Serviço de Aplicativo para outro Ambiente do Serviço de Aplicativo.  Esses tipos de chamadas entre Ambiente do Serviço de Aplicativo também serão tratados como chamadas de "Internet".

O diagrama a seguir mostra um exemplo de uma arquitetura em camadas com aplicativos em um Ambiente do Serviço de Aplicativo (por exemplo, Aplicativos Web de "porta de frente") chamando aplicativos em um segundo Ambiente do Serviço de Aplicativo (por exemplo, aplicativos de API de back-end internos que não devem ser acessados pela Internet). 

![Chamadas entre ambientes do serviço de aplicativo][CallsBetweenAppServiceEnvironments] 

No exemplo acima, o Ambiente do Serviço de Aplicativo "ASE One" tem um endereço IP de saída de 192.23.1.2.  Se um aplicativo em execução nesse Ambiente do Serviço de Aplicativo fizer uma chamada de saída para um aplicativo em execução em um segundo Ambiente do Serviço de Aplicativo ("ASE dois") localizado na mesma rede virtual, a chamada de saída será tratada como uma chamada de "Internet".  Como resultado, o tráfego de rede que chega na segunda Ambiente do Serviço de Aplicativo aparecerá como proveniente de 192.23.1.2 (ou seja, não o intervalo de endereços de sub-rede da primeira Ambiente do Serviço de Aplicativo).

Embora as chamadas entre diferentes ambientes de serviço de aplicativo sejam tratadas como chamadas de "Internet", quando ambos os ambientes de serviço de aplicativo estão localizados na mesma região do Azure, o tráfego de rede permanecerá na rede regional do Azure e não fluirá fisicamente pelo Internet pública.  Como resultado, você pode usar um grupo de segurança de rede na sub-rede da segunda Ambiente do Serviço de Aplicativo para permitir apenas chamadas de entrada do primeiro Ambiente do Serviço de Aplicativo (cujo endereço IP de saída é 192.23.1.2), garantindo assim a comunicação segura entre o aplicativo Ambientes de serviço.

## <a name="additional-links-and-information"></a>Links e informações adicionais
Os detalhes sobre as portas de entrada usadas pelos ambientes do serviço de aplicativo e o uso de grupos de segurança de rede para controlar o tráfego de entrada estão disponíveis [aqui][controllinginboundtraffic].

Detalhes sobre como usar rotas definidas pelo usuário para conceder acesso de Internet de saída a ambientes do serviço de aplicativo estão disponíveis neste [artigo][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

