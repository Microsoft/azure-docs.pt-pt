---
title: O que é o Serviço de Sinalização Azure?
description: Compreenda melhor o que os cenários típicos de utilização usam o Azure SignalR e aprenda os principais benefícios do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 31cb4570dd0952f60fbff2fa1131b3af36c278cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74157591"
---
# <a name="what-is-azure-signalr-service"></a>O que é o Serviço de Sinalização Azure?

O Azure SignalR Service simplifica o processo de acrescentar a funcionalidade Web de tempo real a aplicações através de HTTP. Esta funcionalidade em tempo real permite que o serviço envie atualizações de conteúdo a clientes ligados, como uma única página Web ou aplicação móvel. Como resultado, os clientes são atualizados sem que tenham de consultar o servidor ou submeter novos pedidos HTTP para as atualizações.


Este artigo disponibiliza uma descrição geral do Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Para que serve o Azure SignalR Service?

Qualquer cenário que exija empurrar dados do servidor para o cliente em tempo real, pode utilizar o Serviço De Sinalização Azure.

As funcionalidades tradicionais em tempo real que muitas vezes requerem sondagens a partir do servidor, também podem usar o Serviço De Sinalização Azure.

O Serviço De Sinalização Azure tem sido utilizado numa grande variedade de indústrias, para qualquer tipo de aplicação que exija atualizações de conteúdo em tempo real. Listamos alguns exemplos que são bons para usar o Serviço De Sinalização Azure:

* **Atualizações** de dados de alta frequência: jogos, votação, sondagens, leilão.
* **Dashboards e monitorização:** dashboard da empresa, dados do mercado financeiro, atualização instantânea de vendas, conselho de líder de jogo multi-jogador e monitorização ioT.
* **Chat:** sala de chat ao vivo, chat bot, suporte ao cliente on-line, assistente de compras em tempo real, mensageiro, chat no jogo, e assim por diante.
* **Localização em tempo real no mapa:** rastreio logístico, rastreio do estado de entrega, atualizações do estado de transporte, aplicações gps.
* **Anúncios direcionados em tempo real: anúncios** e ofertas personalizadas em tempo real, anúncios interativos.
* **Aplicativos colaborativos:** coautoria, aplicações de quadro sinuoso e software de reunião de equipas.
* **Notificações push:** rede social, e-mail, jogo, alerta de viagem.
* **Transmissão em tempo real:** transmissão de áudio/vídeo ao vivo, legendagem ao vivo, tradução, eventos/transmissão de notícias.
* **IoT e dispositivos conectados:** métricas ioT em tempo real, controlo remoto, estado em tempo real e rastreio de localização.
* **Automação:** gatilho em tempo real de eventos a montante.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Quais são os benefícios usando o Serviço De Sinalização Azure?

**Baseado em norma:**

O SignalR oferece uma abstração através de um número de técnicas utilizadas para compilar aplicações Web em tempo real. [WebSockets](https://wikipedia.org/wiki/WebSocket) é o transporte ideal, mas podem ser utilizadas outras técnicas como [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) e Long Polling, se não estiverem disponíveis outras opções. O SignalR deteta e inicializa automaticamente o transporte adequado com base nas funcionalidades que o servidor e o cliente suportam.

**Suporte nativo ASP.NET Core:**

O SignalR Service proporciona experiência de programação nativa com ASP.NET Core e ASP.NET. Desenvolver uma nova aplicação SignalR com o SignalR Service, ou migrar da aplicação baseada no SignalR existente para o SignalR Service requer esforços mínimos.
O SignalR Service também suporta ASP.NET nova funcionalidade do Core, O Blazor do lado do servidor.

**Amplo apoio ao cliente:**

O SignalR Service funciona com uma vasta gama de clientes, tais como navegadores web e móveis, aplicações de desktop, aplicações móveis, processo de servidor, dispositivos IoT e consolas de jogos. O SignalR Service oferece SDKs em diferentes idiomas. Além de nativos ASP.NET Core ou ASP.NET C# SDKs, o SignalR Service também fornece o cliente JavaScript SDK, para ativar clientes web, e muitos quadros JavaScript. O sDK do cliente Java também é suportado para aplicações Java, incluindo aplicações nativas Android. O Serviço SignalR suporta a REST API, e servidor através de integrações com funções Azure e Rede de Eventos.

**Manuseie ligações de clientes em larga escala:**

O Serviço SignalR foi concebido para aplicações em tempo real em larga escala. O SignalR Service permite que várias instâncias trabalhem em conjunto para escalar milhões de ligações ao cliente. O serviço também apoia várias regiões globais para fins de sharding, alta disponibilidade ou recuperação de desastres.

**Retire o fardo para o auto-hospedeiro SignalR:**

Em comparação com as aplicações SignalR auto-hospedadas, a mudança para o SignalR Service eliminará a necessidade de gerir os aviões traseiros que lidam com as balanças e as ligações com o cliente. O serviço totalmente gerido também simplifica aplicações web e poupa custos de hospedagem. O SignalR Service oferece alcance global e centro de dados de classe mundial e rede, escala para milhões de conexões, garante a SLA, ao mesmo tempo que fornece toda a conformidade e segurança na norma Azure.

![Serviço de Sinalização Gerido](./media/signalr-overview/managed-signalr-service.png)

**Ofereça APIs ricos para diferentes padrões de mensagens:**

O Serviço SignalR permite que o servidor envie mensagens para uma determinada ligação, todas as ligações ou um subconjunto de ligações que pertencem a um utilizador específico, ou que tenham sido colocadas num grupo arbitrário.

## <a name="how-to-use-azure-signalr-service"></a>Como utilizar o Azure SignalR Service

Existem muitas maneiras diferentes de programar com o Serviço De Sinalização Azure, como algumas das amostras listadas aqui:

- **[Dimensionar uma Aplicação do SignalR do ASP.NET Core](signalr-concept-scale-aspnet-core.md)** - Integrar o Azure SignalR Service numa aplicação do SignalR do ASP.NET Core para aumentar horizontalmente para centenas de milhares de ligações.
- **[Criar aplicações em tempo real sem servidor](signalr-concept-azure-functions.md)** - Utilize a integração das Funções do Azure com o Azure SignalR Service para criar aplicações em tempo real sem servidor em linguagens como JavaScript, C# e Java.
- **[Enviar mensagens de servidor a clientes através da API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** - O Azure SignalR Service apresenta a API REST para permitir que as aplicações publiquem mensagens para clientes ligados com o SignalR Service, em quaisquer linguagens de programação compatíveis com REST.
