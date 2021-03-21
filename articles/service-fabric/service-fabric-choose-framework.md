---
title: Descrição geral do modelo de programação do Service Fabric
description: 'A Service Fabric oferece dois quadros para serviços de construção: a estrutura do ator e o quadro de serviços. Oferecem trocas distintas na simplicidade e no controlo.'
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 523c9e0064d8b78698f1a0dd3544bda58436800c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575725"
---
# <a name="service-fabric-programming-model-overview"></a>Descrição geral do modelo de programação do Service Fabric

A Service Fabric oferece várias formas de escrever e gerir os seus serviços. Os serviços podem optar por utilizar as APIs do Tecido de Serviço para tirar o máximo partido das funcionalidades e dos quadros de aplicação da plataforma. Os serviços também podem ser qualquer programa executável compilado escrito em qualquer idioma ou código que esteja a decorrer num contentor alojado num cluster de Tecido de Serviço.

## <a name="guest-executables"></a>Executáveis de convidado

Um [hóspede executável](service-fabric-guest-executables-introduction.md) é um executável arbitrário existente (escrito em qualquer idioma) que pode ser executado como um serviço na sua aplicação. Os executáveis dos hóspedes não ligam diretamente para as APIs SDK de Tecido de Serviço. No entanto, ainda beneficiam de funcionalidades que a plataforma oferece, como a descoberta do serviço, a saúde personalizada e o relato de carga, chamando ASE DE REST expostas pela Service Fabric. Também têm suporte completo de ciclo de vida de aplicações.

Inicie com os executáveis de hóspedes implementando a sua primeira [aplicação executável de hóspedes](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Contentores

Por predefinição, o Service Fabric implementa e ativa os serviços como processos. O Tecido de Serviço também pode implantar serviços em [contentores.](service-fabric-containers-overview.md) O Service Fabric suporta a colocação de contentores Linux e de Windows no Windows Server 2016 e posteriormente. As imagens do contentor podem ser retiradas de qualquer repositório de contentores e colocadas na máquina. Você pode implementar aplicações existentes como executáveis de hóspedes, serviços apátridas ou stateful Reliable ou atores fiáveis em contentores, e você pode misturar serviços em processos e serviços em recipientes na mesma aplicação.

[Saiba mais sobre a contentorização dos seus serviços no Windows ou Linux](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services é um quadro leve para serviços de escrita que se integram com a plataforma Service Fabric e beneficiam de todo o conjunto de funcionalidades da plataforma. Os Serviços Fiáveis fornecem um conjunto mínimo de APIs que permitem ao Service Fabric gerir o ciclo de vida dos seus serviços e que permitem que os seus serviços interajam com o tempo de funcionação. O quadro de aplicação é mínimo, dando-lhe total controlo sobre escolhas de design e implementação, e pode ser usado para hospedar qualquer outro quadro de aplicação, como ASP.NET Core.

Os Serviços Fiáveis podem ser apátridas, semelhantes à maioria das plataformas de serviço, como servidores web, em que cada instância do serviço é criada de forma igual e o estado é persistido numa solução externa, como Azure DB ou Azure Table Storage.

Exclusivos do Service Fabric, os Serviços Fiáveis também podem ser imponentes, onde o estado é persistido diretamente no próprio serviço usando Coleções Fiáveis. O Estado é altamente disponível através da replicação e distribuído através da partição, tudo gerido automaticamente pela Service Fabric.

[Saiba mais sobre serviços fiáveis](service-fabric-reliable-services-introduction.md) ou [inicie-se escrevendo o seu primeiro Serviço Fiável.](service-fabric-reliable-services-quick-start.md)

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core é um quadro de plataforma cruzada de código aberto para a construção de aplicações modernas baseadas na Internet, tais como aplicações web, aplicações IoT e backends móveis. O Service Fabric integra-se com ASP.NET Core para que possa escrever aplicações de ASP.NET Core apátridas e imponentes que tirem partido das capacidades avançadas de orquestração da Reliable Collections e service fabric.

[Saiba mais sobre ASP.NET Core in Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) ou inicie a sua primeira [aplicação ASP.NET Core Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Construído em cima de Serviços Confiáveis, o quadro de ator fiável é uma estrutura de aplicação que implementa o padrão [de Ator Virtual,](https://research.microsoft.com/en-us/projects/orleans/) baseado no modelo de [ator](https://en.wikipedia.org/wiki/Actor_model)computacional. O quadro de Ator Fiável usa unidades independentes de computação e estado com execução de linha única chamadas *atores*. O quadro de atores fiáveis fornece comunicação incorporada para os atores e configurações de persistência e escala pré-definidas.

Como o Reliable Actors é um quadro de aplicações construído em Serviços Fidedigtos, está totalmente integrado com a plataforma Service Fabric e beneficia de todo o conjunto de funcionalidades oferecidas pela plataforma.

[Saiba mais sobre a reliable actors](service-fabric-reliable-actors-introduction.md) ou começar por escrever o seu primeiro serviço de Ator [Fiável](service-fabric-reliable-actors-get-started.md)

[Construa um serviço frontal utilizando ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Passos seguintes

[Visão geral do tecido de serviço e dos recipientes](service-fabric-containers-overview.md)

[Descrição geral do Reliable Services](service-fabric-reliable-services-introduction.md)

[Descrição geral do Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Tecido de serviço e núcleo de ASP.NET](service-fabric-reliable-services-communication-aspnetcore.md)
