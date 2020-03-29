---
title: Descrição geral do modelo de programação do Service Fabric
description: 'A Service Fabric oferece dois quadros para serviços de construção: o quadro do ator e o quadro de serviços. Oferecem compensações distintas em simplicidade e controlo.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 11e32c9d1290227e638a314ed8417b1bed906842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749528"
---
# <a name="service-fabric-programming-model-overview"></a>Descrição geral do modelo de programação do Service Fabric

O Service Fabric oferece várias formas de escrever e gerir os seus serviços. Os serviços podem optar por utilizar as APIs de Tecido de Serviço para tirar o máximo partido das funcionalidades e quadros de aplicação da plataforma. Os serviços também podem ser qualquer programa executável compilado escrito em qualquer idioma ou código que funciona num recipiente alojado num cluster de Tecido de Serviço.

## <a name="guest-executables"></a>Executáveis de convidado

Um [hóspede executável](service-fabric-guest-executables-introduction.md) é um executável arbitrário existente (escrito em qualquer idioma) que pode ser executado como um serviço na sua aplicação. Executáveis de hóspedes não chamam as APIs sDK de tecido de serviço diretamente. No entanto, ainda beneficiam de funcionalidades que a plataforma oferece, como a descoberta de serviços, a saúde personalizada e o relato de carga, chamando ASAP IS REST expostos pelo Service Fabric. Também têm suporte completo de vida de vida de aplicação.

Inicie-se com executáveis de hóspedes implementando a sua primeira [aplicação executável de hóspedes.](service-fabric-deploy-existing-app.md)

## <a name="containers"></a>Contentores

Por padrão, o Tecido de Serviço implanta e ativa os serviços como processos. Serviço Tecido também pode implementar serviços em [contentores](service-fabric-containers-overview.md). O Service Fabric suporta a implementação de contentores Linux e windows no Windows Server 2016 e posteriormente. As imagens do recipiente podem ser retiradas de qualquer repositório de contentores e implantadas na máquina. Você pode implementar aplicações existentes como executáveis de hóspedes, serviços apátridas ou apátridas confiáveis ou atores fiáveis em contentores, e você pode misturar serviços em processos e serviços em contentores na mesma aplicação.

[Saiba mais sobre a containerização dos seus serviços no Windows ou Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services

A Reliable Services é um quadro leve para serviços de escrita que se integram com a plataforma Service Fabric e beneficiam de todo o conjunto de funcionalidades da plataforma. Os Serviços Fiáveis fornecem um conjunto mínimo de APIs que permitem ao tempo de execução do Tecido de Serviço gerir o ciclo de vida dos seus serviços e que permitem que os seus serviços interajam com o tempo de execução. O quadro de aplicação é mínimo, dando-lhe total controlo sobre as opções de conceção e implementação, e pode ser usado para acolher qualquer outro quadro de aplicação, como ASP.NET Core.

Os Serviços Fiáveis podem ser apátridas, semelhantes à maioria das plataformas de serviços, como servidores web, em que cada instância do serviço é criada igual e o estado é persistido numa solução externa, como o Azure DB ou o Azure Table Storage.

Exclusivo para o Serviço De Tecido, os Serviços Fiáveis também podem ser audacionais, onde o estado é persistido diretamente no próprio serviço usando Coleções Fiáveis. O Estado é altamente disponível através da replicação e distribuído através de divisórias, tudo gerido automaticamente pela Service Fabric.

[Saiba mais sobre serviços fiáveis](service-fabric-reliable-services-introduction.md) ou inicie-se [escrevendo o seu primeiro Serviço Fiável.](service-fabric-reliable-services-quick-start.md)

## <a name="aspnet-core"></a>Núcleo de ASP.NET

ASP.NET Core é um quadro de código aberto e cross-platform para a construção de aplicações modernas ligadas à Internet baseadas na nuvem, tais como aplicações web, aplicações IoT e backends móveis. O Service Fabric integra-se com ASP.NET Core para que possa escrever aplicações apátridas e apátridas ASP.NET Core que aproveitam as capacidades avançadas de orquestração de Coleções Fiáveis e Service Fabric.

[Saiba mais sobre ASP.NET Core em Tecido de Serviço](service-fabric-reliable-services-communication-aspnetcore.md) ou começar por escrever a sua primeira [aplicação ASP.NET Core Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Construído em cima de Serviços Fiáveis, o quadro de Ator Fiável é um quadro de aplicação que implementa o padrão [de Ator Virtual,](https://research.microsoft.com/en-us/projects/orleans/) baseado no modelo de [ator](https://en.wikipedia.org/wiki/Actor_model)computacional. O quadro do Ator Fiável utiliza unidades independentes de computação e estado com execução de um fio único chamado *atores*. O quadro do Ator Fiável fornece comunicação incorporada para atores e persistência de estado pré-definido e configurações de escala para fora.

Como a Reliable Actors é um quadro de aplicação construído em Serviços Fiáveis, está totalmente integrado com a plataforma Service Fabric e beneficia de todo o conjunto de funcionalidades oferecidas pela plataforma.

[Saiba mais sobre Atores Fiáveis](service-fabric-reliable-actors-introduction.md) ou começar escrevendo o seu primeiro serviço de Ator [Fiável](service-fabric-reliable-actors-get-started.md)

[Construa um serviço frontal usando ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Passos seguintes

[Visão geral do tecido de serviço e dos contentores](service-fabric-containers-overview.md)

[Descrição geral do Reliable Services](service-fabric-reliable-services-introduction.md)

[Descrição geral do Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Tecido de serviço e núcleo de ASP.NET](service-fabric-reliable-services-communication-aspnetcore.md)
