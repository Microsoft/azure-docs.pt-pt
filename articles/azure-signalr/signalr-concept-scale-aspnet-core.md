---
title: Escala ASP.NET sinal de núcleo com sinaleiro Azure
description: Uma descrição geral da utilização do serviço Azure SignalR para dimensionar aplicações do SignalR do ASP.NET Core.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: d104e0f1f2c6a978a5fce2c046a36e50a7056970
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928505"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Dimensionar aplicações do SignalR do ASP.NET Core com o Azure SignalR Service

## <a name="developing-signalr-apps"></a>Desenvolver aplicações SignalR

Atualmente, pode utilizar [duas versões](https://docs.microsoft.com/aspnet/core/signalr/version-differences) do SignalR com as suas aplicações Web: SignalR para ASP.NET e ASP.NET Core SignalR, que é a versão mais recente. O Azure SignalR Service é um serviço gerido pelo Azure criado sobre o ASP.NET Core SignalR.

O ASP.NET Core SignalR é uma reescrita da versão anterior. Como resultado, o ASP.NET Core SignalR não é retrocompatível com a versão anterior do SignalR. As APIs e os comportamentos são diferentes. O ASP.NET Core SignalR SDK tem como alvo o .NET Standard, pelo que pode continuar a utilizá-lo com o .NET Framework. No entanto, tem de utilizar as APIs novas em vez das antigos. Se estiver a utilizar o SignalR e quiser passar para o ASP.NET Core SignalR ou para o Azure SignalR, tem de alterar o código para que este possa lidar com as diferenças nas APIs.

Com o Azure SignalR Service, o componente do lado do servidor do ASP.NET Core SignalR é alojado no Azure. Contudo, uma vez que a tecnologia é criada sobre o ASP.NET Core, tem a possibilidade de executar a aplicação Web real em várias plataformas (Windows, Linux e MacOS) enquanto aloja com o [Serviço de Aplicações do Azure](../app-service/overview.md), o [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), o [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), o [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) e o [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Também pode utilizar o alojamento automático no seu próprio processo.

Se os objetivos da sua aplicação incluírem suportar a mais recente funcionalidade para atualizar clientes Web com atualizações de conteúdos em tempo real, executar em várias plataformas (Azure, Windows, Linux e macOS) e alojar em diferentes ambientes, a melhor opção é tirar partido do Azure SignalR Service.

## <a name="why-not-deploy-signalr-myself"></a>Por que não implementar o SignalR eu próprio?

Implementar a sua própria aplicação Web do Azure que suporte o ASP.NET Core SignalR como componente do back-end na sua aplicação Web geral continua a ser uma abordagem válida.

Um dos principais motivos para utilizar o Azure SignalR Service é a simplicidade. Com o Azure SignalR Service, não tem de lidar com problemas como desempenho, escalabilidade e disponibilidade. Estes problemas são tratados por si com um contrato de nível de serviço de 99,9%.

Além disso, os WebSockets são, normalmente, a técnica preferencial para suportar as atualizações de conteúdos em tempo real. No entanto, à medida que dimensiona, fazer o balanceamento de carga de um grande número de ligações persistentes de WebSocket pode tornar-se problemático. As soluções comuns tiram partido do balanceamento de carga do DNS, dos balanceadores de carga do hardware e do balanceamento de carga do software. O Azure SignalR Service lida com este problema por si.

Outro motivo poderá ser o facto de que talvez não tenha necessidade de realmente alojar uma aplicação Web de todo. A lógica da aplicação Web poderá tirar partido da [computação sem servidor](https://azure.microsoft.com/overview/serverless-computing/). Por exemplo, talvez o código só seja alojado e executado a pedido com acionadores das [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/). Este cenário pode ser complicado, porque o seu código só é executado a pedido e não mantém ligações longas com os clientes. O Azure SignalR Service pode tratar desta situação, uma vez que já faz a gestão das ligações por si. Veja a [descrição geral sobre como utilizar o SignalR Service com as Funções do Azure](signalr-concept-azure-functions.md) para obter mais detalhes.

## <a name="how-does-it-scale"></a>Como é dimensionado?

É comum escalar o SignalR com SQL Server, Azure Service Bus ou Azure Cache para Redis. O Azure SignalR Service lida com a abordagem de dimensionamento por si. O desempenho e o custo são comparáveis a estas abordagens, sem a complexidade de ter de lidar com estes outros serviços. Tudo o que tem de fazer é atualizar a contagem de unidades do seu serviço. Cada unidade suporta até 1000 ligações de cliente.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Create a chat room with Azure SignalR](signalr-quickstart-dotnet-core.md) (Início rápido: Criar uma sala de chat com o Azure SignalR)