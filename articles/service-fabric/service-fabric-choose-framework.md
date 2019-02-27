---
title: Visão geral do modelo de programação do Service Fabric | Documentos da Microsoft
description: 'Service Fabric oferece duas estruturas para a criação de serviços: a estrutura de ator e a estrutura de serviços. Eles oferecem vantagens e desvantagens distintas na simplicidade e controlo.'
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: d791148e6ee023239b0a6bf49c705c2da4eea9a2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881877"
---
# <a name="service-fabric-programming-model-overview"></a>Visão geral do modelo programação Service Fabric
Service Fabric oferece várias maneiras de escrever e gerir os seus serviços. Serviços podem optar por utilizar as APIs de recursos de infraestrutura do serviço para tirar partido das funcionalidades e estruturas de aplicativo da plataforma. Os serviços também podem ser qualquer programa executável compilado escrito em qualquer linguagem ou o código em execução num contentor alojado num cluster do Service Fabric.

## <a name="guest-executables"></a>Executáveis de convidado
R [executável convidado](service-fabric-guest-executables-introduction.md) é uma existente, o executável arbitrário (escrito em qualquer linguagem) que pode ser executado como um serviço na sua aplicação. Executáveis de convidado não chamar diretamente as APIs do SDK do Service Fabric. Entretanto eles ainda beneficiam de recursos oferece a plataforma, como a capacidade de deteção do serviço, estado de funcionamento personalizado e carregar relatórios ao chamar APIs REST exposta pelo Service Fabric. Também têm suporte de ciclo de vida do aplicativo completo.

Introdução ao executáveis de convidado ao implementar a sua primeira [aplicação executável convidada](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Contentores
Por predefinição, o Service Fabric implementa e ativa a serviços como processos. Service Fabric também pode implementar serviços no [contentores](service-fabric-containers-overview.md). Service Fabric suporta a implementação de contentores do Linux e contentores do Windows no Windows Server 2016. Imagens de contentor podem ser obtidas a partir de qualquer repositório de contentor e implementadas a máquina. Pode implantar os aplicativos existentes como executáveis convidados, o Service Fabric com ou sem estado Reliable services ou o Reliable Actors em contentores, e pode combinar serviços em processos e serviços em contentores no mesmo aplicativo.

[Saiba mais sobre como empacotar os seus serviços no Windows ou Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Reliable Services é uma estrutura leve para serviços que se integram com a plataforma do Service Fabric e o conjunto completo de funcionalidades de plataforma se beneficiar de escrever. Reliable Services fornecem um conjunto mínimo de APIs que permitem que o runtime do Service Fabric gerir o ciclo de vida dos seus serviços e permitir que seus serviços interagir com o tempo de execução. A estrutura da aplicação é mínima, dando-lhe controlo total sobre as opções de design e implementação e pode ser utilizada para alojar qualquer outra estrutura de aplicativo, como o ASP.NET Core.

Reliable Services pode ser sem estado, semelhante a maioria das plataformas de serviço, tais como servidores web, na qual cada instância do serviço é criada igual e estado é mantido numa solução externa, como o Azure DB ou o armazenamento de tabelas do Azure.

Também pode ser com monitoração de estado, Reliable Services exclusivo para o Service Fabric, em que estado é mantido diretamente no serviço do próprio utilizando as coleções fiáveis. Estado é feito a elevada disponibilidade através da replicação e distribuído através da criação de partições, todos os geridos automaticamente pelo Service Fabric.

[Saiba mais sobre o Reliable Services](service-fabric-reliable-services-introduction.md) ou começar a utilizar ao [escrever seu primeiro serviço fiável](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>Núcleo de ASP.NET
O ASP.NET Core é uma nova estrutura de código-fonte aberto e Multiplataforma para a criação de modernas aplicações baseadas na nuvem ligada à Internet, como aplicações web, aplicações de IoT e back-ends móveis. Service Fabric integra-se com o ASP.NET Core para que pode escrever com e sem estado ASP.NET Core a aplicações que tiram partido das coleções fiáveis e de recursos de orquestração avançada do Service Fabric.

[Saiba mais sobre o ASP.NET Core no Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) ou começar a utilizar ao [escrever seu primeiro aplicativo do ASP.NET Core Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors
Criados sobre Reliable Services, o framework de Reliable Actor é uma estrutura de aplicações que implementa o padrão de Ator Virtual, com base no padrão de design de ator. A estrutura de Reliable Actor usa unidades independentes de computação e o estado com a execução de thread único chamada atores. A estrutura de Reliable Actor fornece comunicação incorporada para atores e configurações de escalamento horizontal e persistência de estado predefinidas.

Como o Reliable Actors é uma arquitetura de aplicação incorporada no Reliable Services, ele é totalmente integrado com a plataforma do Service Fabric e os benefícios do conjunto completo de funcionalidades oferecidos pela plataforma.

[Saiba mais sobre o Reliable Actors](service-fabric-reliable-actors-introduction.md) ou começar a utilizar ao [escrever seu primeiro serviço dos Reliable Actors](service-fabric-reliable-actors-get-started.md)


[Criar um serviço de front-end com o ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Passos Seguintes
[Descrição geral do Service Fabric e contentores](service-fabric-containers-overview.md)

[Descrição geral do Reliable Services](service-fabric-reliable-services-introduction.md)

[Descrição geral do Reliable Actors](service-fabric-reliable-actors-introduction.md)

[O Service Fabric e ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)




