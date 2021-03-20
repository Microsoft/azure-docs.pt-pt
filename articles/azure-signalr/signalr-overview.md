---
title: O que é o Azure SignalR Service?
description: Entenda melhor quais os cenários típicos de uso para usar O Sinal Azure e aprenda os principais benefícios do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 92da400616ace9f24da121d81798d5ebd9222e35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88921890"
---
# <a name="what-is-azure-signalr-service"></a>O que é o Azure SignalR Service?

O Azure SignalR Service simplifica o processo de acrescentar a funcionalidade Web de tempo real a aplicações através de HTTP. Esta funcionalidade em tempo real permite que o serviço envie atualizações de conteúdo a clientes ligados, como uma única página Web ou aplicação móvel. Como resultado, os clientes são atualizados sem que tenham de consultar o servidor ou submeter novos pedidos HTTP para as atualizações.


Este artigo disponibiliza uma descrição geral do Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Para que serve o Azure SignalR Service?

Qualquer cenário que exija empurrar dados do servidor para o cliente em tempo real, pode utilizar o Serviço Azure SignalR.

As funcionalidades tradicionais em tempo real que muitas vezes requerem sondagens a partir do servidor, também podem usar o Serviço Azure SignalR.

O Serviço Azure SignalR tem sido utilizado em uma grande variedade de indústrias, para qualquer tipo de aplicação que exija atualizações de conteúdo em tempo real. Listamos alguns exemplos que são bons para usar o Serviço Azure SignalR:

* **Atualizações de dados de alta frequência:** jogos, votação, sondagens, leilão.
* **Dashboards e monitorização:** painel de instrumentos da empresa, dados do mercado financeiro, atualização instantânea de vendas, painel de líderes de jogos multi-jogadores e monitorização de IoT.
* **Chat:** chat room ao vivo, chat bot, suporte ao cliente on-line, assistente de compras em tempo real, mensageiro, chat no jogo, e assim por diante.
* **Localização em tempo real no mapa:** rastreio logístico, rastreio do estado de entrega, atualizações do estado do transporte, aplicações de GPS.
* **Anúncios direcionados em tempo real:** anúncios e ofertas de push em tempo real personalizados, anúncios interativos.
* **Aplicativos colaborativos:** coautoria, aplicações de quadros e software de reunião de equipas.
* **Notificações push:** rede social, e-mail, jogo, alerta de viagem.
* **Radiodifusão em tempo real:** transmissão áudio/vídeo ao vivo, legendas ao vivo, tradução, eventos/notícias de radiodifusão.
* **Dispositivos IoT e conectados:** métricas de IoT em tempo real, controlo remoto, estado em tempo real e rastreio de localização.
* **Automação:** gatilho em tempo real de eventos a montante.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Quais são os benefícios usando o Serviço Azure SignalR?

**Com base na norma:**

O SignalR oferece uma abstração através de um número de técnicas utilizadas para compilar aplicações Web em tempo real. [WebSockets](https://wikipedia.org/wiki/WebSocket) é o transporte ideal, mas podem ser utilizadas outras técnicas como [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) e Long Polling, se não estiverem disponíveis outras opções. O SignalR deteta e inicializa automaticamente o transporte adequado com base nas funcionalidades que o servidor e o cliente suportam.

**Suporte do Núcleo de ASP.NET Nativo:**

O Serviço SignalR proporciona experiência de programação nativa com ASP.NET Core e ASP.NET. Desenvolver uma nova aplicação SignalR com o Serviço SignalR, ou migrar da aplicação baseada em SignalR para o Serviço SignalR requer esforços mínimos.
O Serviço SignalR também suporta ASP.NET nova funcionalidade do Core, o Blazor do lado do servidor.

**Amplo apoio ao cliente:**

O SignalR Service funciona com uma vasta gama de clientes, tais como navegadores web e móveis, aplicações de desktop, aplicações móveis, processo de servidor, dispositivos IoT e consolas de jogos. O SignalR Service oferece SDKs em diferentes idiomas. Além de ASP.NET nativos Core ou ASP.NET SDKs C#, o SignalR Service também fornece SDK ao cliente JavaScript, para ativar clientes web e muitas estruturas JavaScript. O cliente Java SDK também é suportado para aplicações Java, incluindo aplicações nativas do Android. O Serviço SignalR suporta a API REST e sem servidor através de integrações com funções Azure e Grade de Eventos.

**Lidar com ligações de clientes em larga escala:**

O Serviço SignalR foi concebido para aplicações em larga escala em tempo real. O Serviço SignalR permite que várias instâncias trabalhem em conjunto para escalar para milhões de ligações ao cliente. O serviço também apoia várias regiões globais para fins de sharding, alta disponibilidade ou recuperação de desastres.

**Retire o fardo para o auto-hospedeiro SignalR:**

Em comparação com as aplicações SignalR auto-hospedadas, a mudança para o Serviço SignalR removerá a necessidade de gerir os planos traseiros que manuseiam as balanças e as ligações do cliente. O serviço totalmente gerido também simplifica as aplicações web e poupa o custo de hospedagem. O SignalR Service oferece um centro de dados e um centro de dados de classe mundial, escalas para milhões de conexões, garante o SLA, ao mesmo tempo que fornece toda a conformidade e segurança ao padrão Azure.

![Serviço SignalR gerido](./media/signalr-overview/managed-signalr-service.png)

**Ofereça APIs ricos para diferentes padrões de mensagens:**

O Serviço SignalR permite que o servidor envie mensagens para uma determinada ligação, todas as ligações ou um subconjunto de ligações que pertencem a um utilizador específico, ou que tenham sido colocadas num grupo arbitrário.

## <a name="how-to-use-azure-signalr-service"></a>Como utilizar o Azure SignalR Service

Existem muitas maneiras diferentes de programar com o Serviço Azure SignalR, como algumas das amostras listadas aqui:

- **[Dimensionar uma Aplicação do SignalR do ASP.NET Core](signalr-concept-scale-aspnet-core.md)** - Integrar o Azure SignalR Service numa aplicação do SignalR do ASP.NET Core para aumentar horizontalmente para centenas de milhares de ligações.
- **[Criar aplicações em tempo real sem servidor](signalr-concept-azure-functions.md)** - Utilize a integração das Funções do Azure com o Azure SignalR Service para criar aplicações em tempo real sem servidor em linguagens como JavaScript, C# e Java.
- **[Enviar mensagens de servidor a clientes através da API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** - O Azure SignalR Service apresenta a API REST para permitir que as aplicações publiquem mensagens para clientes ligados com o SignalR Service, em quaisquer linguagens de programação compatíveis com REST.
