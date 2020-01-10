---
title: Descrição geral do modelo de programação do Service Fabric
description: 'O Service Fabric oferece duas estruturas para a criação de serviços: a estrutura de ator e a estrutura de serviços. Eles oferecem compensações distintas em simplicidade e controle.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 11e32c9d1290227e638a314ed8417b1bed906842
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749528"
---
# <a name="service-fabric-programming-model-overview"></a>Descrição geral do modelo de programação do Service Fabric

O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços. Os serviços podem optar por usar as APIs de Service Fabric para aproveitar ao máximo os recursos e as estruturas de aplicativo da plataforma. Os serviços também podem ser qualquer programa executável compilado escrito em qualquer linguagem ou código em execução em um contêiner hospedado em um Cluster Service Fabric.

## <a name="guest-executables"></a>Executáveis de convidado

Um [executável convidado](service-fabric-guest-executables-introduction.md) é um executável existente e arbitrário (escrito em qualquer linguagem) que pode ser executado como um serviço em seu aplicativo. Os executáveis convidados não chamam as APIs SDK do Service Fabric diretamente. No entanto, eles ainda se beneficiam dos recursos oferecidos pela plataforma, como descoberta de serviço, integridade personalizada e relatórios de carga chamando APIs REST expostas por Service Fabric. Eles também têm suporte total ao ciclo de vida do aplicativo.

Introdução aos executáveis convidados implantando seu primeiro [aplicativo executável convidado](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Contentores

Por padrão, o Service Fabric implanta e ativa serviços como processos. Service Fabric também pode implantar serviços em [contêineres](service-fabric-containers-overview.md). Service Fabric dá suporte à implantação de contêineres do Linux e contêineres do Windows no Windows Server 2016 e posterior. As imagens de contêiner podem ser extraídas de qualquer repositório de contêiner e implantadas no computador. Você pode implantar aplicativos existentes como executáveis convidados, Service Fabric serviços confiáveis com ou sem estado ou Reliable Actors em contêineres, e pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo.

[Saiba mais sobre o contêiner de seus serviços no Windows ou no Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services é uma estrutura leve para escrever serviços que se integram à plataforma de Service Fabric e se beneficiam do conjunto completo de recursos de plataforma. Reliable Services fornecer um conjunto mínimo de APIs que permitem que o tempo de execução de Service Fabric gerencie o ciclo de vida de seus serviços e que os serviços interajam com o tempo de execução. A estrutura do aplicativo é mínima, dando a você controle total sobre as opções de design e implementação e pode ser usada para hospedar qualquer outra estrutura de aplicativo, como ASP.NET Core.

Reliable Services pode ser sem monitoração de estado, semelhante à maioria das plataformas de serviço, como servidores Web, em que cada instância do serviço é criada igual e o estado é persistido em uma solução externa, como o Azure DB ou o armazenamento de tabelas do Azure.

Exclusivo para Service Fabric, o Reliable Services também pode ser com estado, em que o estado é persistido diretamente no próprio serviço usando coleções confiáveis. O estado se torna altamente disponível por meio da replicação e distribuído por meio de particionamento, tudo gerenciado automaticamente pelo Service Fabric.

[Saiba mais sobre Reliable Services](service-fabric-reliable-services-introduction.md) ou comece [escrevendo seu primeiro serviço confiável](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core é uma estrutura de plataforma cruzada de software livre para criar aplicativos conectados à Internet modernos baseados em nuvem, como aplicativos Web, aplicativos de IoT e back-ends móveis. O Service Fabric integra-se com o ASP.NET Core para que você possa escrever aplicativos de ASP.NET Core com e sem estado que aproveitam as coleções confiáveis e os recursos de orquestração avançados de Service Fabric.

[Saiba mais sobre ASP.NET Core em Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) ou comece [escrevendo seu primeiro aplicativo ASP.NET Core Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Criado sobre o Reliable Services, a estrutura de ator confiável é uma estrutura de aplicativo que implementa o padrão de [ator virtual](https://research.microsoft.com/en-us/projects/orleans/) , com base no [modelo de ator](https://en.wikipedia.org/wiki/Actor_model)computacional. A estrutura de ator confiável usa unidades independentes de computação e estado com execução de thread único chamada *atores*. A estrutura de ator confiável fornece comunicação interna para atores e configurações de expansão e persistência de estado predefinido.

Como Reliable Actors é uma estrutura de aplicativo criada em Reliable Services, ela é totalmente integrada à plataforma de Service Fabric e se beneficia do conjunto completo de recursos oferecidos pela plataforma.

[Saiba mais sobre Reliable Actors](service-fabric-reliable-actors-introduction.md) ou comece [escrevendo seu primeiro serviço de ator confiável](service-fabric-reliable-actors-get-started.md)

[Criar um serviço de front-end usando o ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Passos seguintes

[Visão geral de Service Fabric e contêineres](service-fabric-containers-overview.md)

[Visão geral de Reliable Services](service-fabric-reliable-services-introduction.md)

[Visão geral de Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric e ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
