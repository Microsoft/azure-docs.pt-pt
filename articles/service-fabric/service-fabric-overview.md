---
title: Descrição do Service Fabric no Azure | Microsoft Docs
description: Uma descrição geral do Service Fabric, onde as aplicações são compostas por muitos microsserviços, de modo a proporcionar dimensionamento e resiliência. O Service Fabric é uma plataforma de sistemas distribuídos que é utilizada para criar aplicações escaláveis, fiáveis e facilmente geridas para a cloud.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: 8f2a3536b7d1b02f124b3116c96ce442383a6b3b
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294438"
---
# <a name="overview-of-azure-service-fabric"></a>Descrição Geral do Azure Service Fabric
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços e contentores dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações nativas da cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e fáceis de gerir. O Service Fabric representa a plataforma de próxima geração para criar e gerir estas aplicações empresariais à escala da cloud executadas em contentores.

Este vídeo breve introduz o Service Fabric e os microsserviços:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="applications-composed-of-microservices"></a>Aplicações compostas por microsserviços 
O Service Fabric permite-lhe criar e gerir aplicações dimensionáveis e fiáveis compostas por microsserviços e que são executadas a uma elevada densidade em conjuntos partilhados de máquinas, que são conhecidos como clusters. Disponibiliza um runtime sofisticado e simples para criar microsserviços distribuídos, dimensionáveis, com e sem monitorização de estado e executados em contentores. Também proporciona capacidades abrangentes de gestão para aprovisionar, implementar, monitorizar, atualizar/corrigir e eliminar aplicações implementadas, incluindo serviços contentorizados.

Atualmente, o Service Fabric está subjacente a muitos serviços da Microsoft, entre os quais a Base de Dados SQL do Azure, o Azure Cosmos DB, o Cortana, o Microsoft Power BI, o Microsoft Intune, os Hubs de Eventos do Azure, o Hub IoT do Azure, o Dynamics 365, o Skype para Empresas e muitos dos mais importantes serviços do Azure.

O Service Fabric foi concebido para criar serviços cloud nativos que podem começar por ser pequenos, conforme necessário, e crescer massivamente, com centenas ou milhares de máquinas.

Os serviços à escala da Internet atuais são compostos por microsserviços. Alguns exemplos destes microsserviços incluem gateways de protocolos, perfis de utilizadores, carrinhos de compras, processamento de inventários, filas e caches. O Service Fabric é uma plataforma de microsserviços (ou contentores) que lhes confere um nome exclusivo que pode ter ou não monitorização de estado.

O Service Fabric disponibiliza capacidades abrangentes de gestão de runtimes e ciclo de vida para as aplicações compostas por estes microsserviços. Aloja-os dentro de contentores que são implementados e ativados no cluster do Service Fabric. A mudança das máquinas virtuais para os contentores possibilita um aumento de ordem de grandeza na densidade. Da mesma forma, se mudar dos contentores para os microsserviços nestes contentores, é possibilitada outra ordem de grandeza na densidade. Por exemplo, um cluster individual da Base de Dados SQL do Azure compreende centenas de máquinas que executam dezenas de milhares de contentores, os quais alojam um total de centenas de milhares de bases de dados. Cada base de dados é um microsserviço do Service Fabric com monitorização de estado. 

Para saber mais sobre a abordagem de microsserviços, leia [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Porquê seguir a abordagem de microsserviços para criar aplicações?).

## <a name="container-deployment-and-orchestration"></a>Orquestração e implementação de contentores
O Service Fabric é o [orquestrador de contentores](service-fabric-cluster-resource-manager-introduction.md) da Microsoft que implementa microsserviços em clusters de máquinas. Os microsserviços podem ser desenvolvidos de muitas formas, desde a utilização dos [modelos de programação do Service Fabric](service-fabric-choose-framework.md) e do [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) até à implementação de [qualquer código à sua escolha](service-fabric-guest-executables-introduction.md). Mais importante, pode misturar ambos os serviços em processos e serviços em contentores na mesma aplicação. Se quiser apenas [implementar e gerir contentores](service-fabric-containers-overview.md), o Service Fabric é o orquestrador de contentores de eleição.

## <a name="any-os-any-cloud"></a>Qualquer SO, qualquer cloud
O Service Fabric é executado em todo o lado. Pode criar clusters para o Service Fabric em muitos ambientes, incluindo no Azure ou no local, no Windows Server ou no Linux. Pode ainda criar clusters noutras clouds públicas. Além disso, o ambiente de desenvolvimento no SDK é **idêntico** ao ambiente de produção, sem emuladores envolvidos. Por outras palavras, o que é executado no seu cluster de desenvolvimento local é implementado nos clusters nos outros ambientes.

![Plataforma do Service Fabric][Image1]

Para desenvolvimento no Windows, o SDK .NET do Service Fabric está integrado no Visual Studio e no PowerShell. Veja [(Preparar o ambiente de desenvolvimento no Windows](service-fabric-get-started.md). Para desenvolvimento no Linux, o SDK Java do Service Fabric está integrado no Eclipse e é utilizado o Yeoman para gerar modelos para aplicações Java, .NET Core e de contentor. Veja [Preparar o ambiente de desenvolvimento o Linux](service-fabric-get-started-linux.md).

Para obter mais informações sobre a criação de clusters, leia [Creating a cluster on Windows Server or Linux](service-fabric-deploy-anywhere.md) (Criar clusters no Windows Server ou no Linux) ou saiba como criá-los no Azure [através do portal do Azure](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microsserviços com e sem monitorização de estado para o Service Fabric
O Service Fabric permite-lhe criar aplicações que consistem em microsserviços ou contentores. Os microsserviços sem estado (como gateways de protocolos e proxies Web) não mantêm um estado mutável fora dos pedidos nem na respetiva resposta do serviço. As funções de trabalho dos Serviços Cloud do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (como contas de utilizador, bases de dados, dispositivos, carrinhos de compras e filas) mantêm um estado mutável e autoritativo para lá do pedido e da respetiva resposta. As aplicações à escala da cloud dos nossos dias são compostas por uma combinação de microsserviços com e sem estado. 

Das principais diferenciações do Service Fabric é que se concentra na criação de serviços com estado, qualquer um deles com o [modelos de programação incorporados](service-fabric-choose-framework.md) ou com serviços com estado contentorizados. Os [cenários de aplicação](service-fabric-application-scenarios.md) descrevem os cenários em que são utilizados os serviços com estado.


## <a name="application-lifecycle-management"></a>Gestão do ciclo de vida das aplicações
O Service Fabric proporciona suporte para todo o ciclo de vida das aplicações e para a CI/CD das aplicações na cloud, incluindo contentores. Este ciclo de vida inclui o desenvolvimento através da implementação, da gestão diária e da manutenção até à desativação final.

As capacidades de gestão do ciclo de vida das aplicações do Service Fabric permitem que os administradores de aplicações e os profissionais de TI utilizem fluxos de trabalho simples e com pouca interação para aprovisionar, implementar, corrigir e monitorizar aplicações. Estes fluxos de trabalho incorporados reduzem significativamente a carga sobre os profissionais de IT para manterem as aplicações disponíveis continuamente.

A maioria das aplicações é constituída por uma combinação de microsserviços com e sem estado, contentores e outros executáveis que são implementados em conjunto. Ao ter tipos seguros nas aplicações, o Service Fabric possibilita a implementação de múltiplas instâncias das aplicações. Cada instância é gerida e atualizada independentemente. Mais importante, pode implementar contentores ou executáveis e torná-los fiáveis. Por exemplo, o Service Fabric pode implementar .NET, ASP.NET Core, node.js, contentores do Windows, contentores do Linux, máquinas virtuais Java, scripts, Angular ou literalmente tudo o que constitui a sua aplicação.

O Service Fabric está integrado com ferramentas de CI/CD como o [Azure Pipelines](https://www.visualstudio.com/team-services/), o [Jenkins](https://jenkins.io/index.html) e o [Octopus Deploy](https://octopus.com/) e pode ser utilizado com qualquer outra ferramenta de CI/CD popular.

Para obter mais informações sobre a gestão do ciclo de vida das aplicações, leia [Application lifecycle](service-fabric-application-lifecycle.md) (Ciclo de vida das aplicações). Para obter mais informações sobre como implementar qualquer código, veja [Deploy a guest executable](service-fabric-deploy-existing-app.md) (Implementar um executável convidado).

## <a name="key-capabilities"></a>Principais capacidades
Ao utilizar o Service Fabric, pode:

* Implementar em datacenters do Azure ou no local que executem Windows ou Linux sem alterações ao código. Escrever uma vez e, depois, implementar em qualquer parte de qualquer cluster do Service Fabric.
* Desenvolver aplicações dimensionáveis compostas por microsserviços, ao utilizar os modelos de programação do Service Fabric, os contentores ou qualquer código.
* Desenvolver microsserviços com e sem estado altamente fiáveis. Simplificar o design das suas aplicações mediante a utilização dos microsserviços com estado. 
* Utilizar o novo modelo de programação Reliable Actors para criar objetos da cloud com código e estado autocontido.
* Implementar e orquestrar contentores que incluem contentores do Windows e do Linux. O Service Fabric é um orquestrador de contentores com deteção de dados e estado.
* Implementar aplicações em segundos, com elevada densidade e centenas ou milhares de aplicações ou contentores por máquina.
* Implementar versões diferentes da mesma aplicação lado a lado e atualizar cada aplicação de forma independente.
* Gerir o ciclo de vida das suas aplicações sem qualquer período de inatividade, incluindo atualizações forçadas e não forçadas.
* Aumentar ou reduzir horizontalmente o número de nós nos clusters. Quando dimensiona os nós, as aplicações são dimensionadas automaticamente.
* Monitorizar e diagnosticar o estado de funcionamento das suas aplicações e definir políticas para realizar reparações automáticas.
* Fazer com que o balanceador de recursos orquestre a redistribuição das aplicações nos clusters. O Service Fabric recupera de falhas e otimiza a distribuição da carga com base nos recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações:
  * [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Porquê seguir a abordagem de microsserviços para criar aplicações?)
  * [Terminology overview](service-fabric-technical-overview.md) (Descrição geral da terminologia)
* Preparar o [ambiente de desenvolvimento no Windows](service-fabric-get-started.md)  
* Preparar o [ambiente de desenvolvimento no Linux](service-fabric-get-started-linux.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
