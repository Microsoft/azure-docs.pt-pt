---
title: O que é o serviço Azure SignalR?
description: Uma visão geral do serviço Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 06/20/2019
ms.author: zhshang
ms.openlocfilehash: dc7ba3585ec49921c0a0e66185fc5550d3d4a006
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303620"
---
# <a name="what-is-azure-signalr-service"></a>O que é o serviço Azure SignalR?

O Azure SignalR Service simplifica o processo de acrescentar a funcionalidade Web de tempo real a aplicações através de HTTP. Esta funcionalidade em tempo real permite que o serviço envie atualizações de conteúdo a clientes ligados, como uma única página Web ou aplicação móvel. Como resultado, os clientes são atualizados sem que tenham de consultar o servidor ou submeter novos pedidos HTTP para as atualizações.


Este artigo disponibiliza uma descrição geral do Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Para que serve o Azure SignalR Service?

Qualquer cenário que requeira a enviar dados a partir do servidor ao cliente em tempo real, pode utilizar o serviço Azure SignalR.

Tradicionais em tempo real recursos que, muitas vezes, necessitam de sondagem do servidor, também pode utilizar o serviço Azure SignalR.

Serviço Azure SignalR foi usado numa ampla variedade de indústrias, para qualquer tipo de aplicação que necessita de atualizações de conteúdo em tempo real. Listamos alguns exemplos que são bons utilizar o serviço Azure SignalR:

* **Atualizações de dados de elevada frequência:** leilões de jogos, votar, consulta,.
* **Dashboards e monitorização:** da empresa dashboard, dados de mercado financeiro, atualização de vendas instantânea, quadro líder de jogos de vários jogadores e monitorização de IoT.
* **Bate-papo:** live sala de bate-papo, chatbot, suporte ao cliente online, Assistente de compra em tempo real, messenger, chat no jogo e assim por diante.
* **Localização em tempo real no mapa:** controlo logística, controlo de estado de entrega, as atualizações de estado de transporte, aplicações GPS.
* **Em tempo real visados anúncios:** personalizadas de anúncios de push de tempo de leitura e ofertas, anúncios interativos.
* **Aplicações de colaboração:** coautoria, aplicações de quadro de comunicações e a equipe de software de reunião.
* **Notificações push:** rede social, o e-mail, o jogo, o alerta de viagem.
* **Difusão em tempo real:** live difusão de áudio/vídeo, estar a legenda codificada, tradução, difusão de eventos/notícias.
* **IoT e dispositivos conectados:** métricas de IoT em tempo real, o controlo remoto, o estado em tempo real e o controle de localização.
* **Automatização:** acionador em tempo real dos eventos a montante.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Quais são os benefícios com o serviço Azure SignalR?

**Standard com base em:**

O SignalR oferece uma abstração através de um número de técnicas utilizadas para compilar aplicações Web em tempo real. [WebSockets](https://wikipedia.org/wiki/WebSocket) é o transporte ideal, mas podem ser utilizadas outras técnicas como [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) e Long Polling, se não estiverem disponíveis outras opções. O SignalR deteta e inicializa automaticamente o transporte adequado com base nas funcionalidades que o servidor e o cliente suportam.

**Suporte nativo do ASP.NET Core:**

Serviço SignalR fornece uma experiência de programação nativa com o ASP.NET Core e o ASP.NET. Desenvolvimento de aplicativo de SignalR novo com o serviço SignalR ou a migrar do SignalR existente aplicação com base no serviço SignalR requer um esforço mínimo.
Serviço SignalR também suporta a funcionalidade nova do ASP.NET Core, Blazor do lado do servidor.

**Suporte abrangente de cliente:**

Serviço SignalR funciona com uma ampla gama de clientes, tais como web e o browsers para dispositivos móveis, aplicativos de desktop, aplicações móveis, o processo do servidor, dispositivos IoT e consolas de jogos. Serviço SignalR oferece SDKs em idiomas diferentes. Para além do ASP.NET Core nativo ou do ASP.NET C# SDKs, o serviço SignalR também fornece SDK, para permitir que os clientes web e muitas estruturas JavaScript de cliente de JavaScript. SDK de cliente Java também é suportada para aplicações de Java, incluindo as aplicações Android nativas. O serviço SignalR suporta a API REST e sem servidor através de integrações com as funções do Azure e o Event Grid.

**Lidar com conexões de cliente em larga escala:**

Serviço SignalR foi concebido para aplicações em tempo real em grande escala. Serviço SignalR permite que várias instâncias para funcionarem em conjunto de dimensionamento para milhões de ligações de cliente. O serviço também suporta várias regiões globais para fins de recuperação após desastre, elevada disponibilidade ou fragmentação.

**Remova a carga para a hospedagem SignalR:**

Em comparação com aplicativos do SignalR autoalojados, mudar para o serviço SignalR irá remover a necessidade de gerir planos de back-que processam as escalas e ligações de cliente. O serviço completamente gerido também simplifica a aplicativos web e guarda o custo de alojamento. Serviço SignalR oferece alcance global e o Centro de dados de nível mundial e rede, pode ser dimensionada para milhões de conexões, garante SLA, ao mesmo tempo, a conformidade e segurança em standard do Azure.

![Serviço gerido do SignalR](./media/signalr-overview/managed-signalr-service.png)

**APIs avançadas para diferentes padrões de mensagens da oferta:**

Serviço SignalR permite ao servidor enviar mensagens para uma ligação específica, todas as ligações ou um subconjunto de ligações que pertencem a um utilizador específico, ou que foram colocadas num grupo de arbitrário.

## <a name="how-to-use-azure-signalr-service"></a>Como utilizar o Azure SignalR Service

Existem várias formas diferentes de programa com o serviço Azure SignalR como alguns dos exemplos listados aqui:

- **[Dimensionar uma Aplicação do SignalR do ASP.NET Core](signalr-concept-scale-aspnet-core.md)** - Integrar o Azure SignalR Service numa aplicação do SignalR do ASP.NET Core para aumentar horizontalmente para centenas de milhares de ligações.
- **[Criar aplicações em tempo real sem servidor](signalr-concept-azure-functions.md)** - Utilize a integração das Funções do Azure com o Azure SignalR Service para criar aplicações em tempo real sem servidor em linguagens como JavaScript, C# e Java.
- **[Enviar mensagens de servidor a clientes através da API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** - O Azure SignalR Service apresenta a API REST para permitir que as aplicações publiquem mensagens para clientes ligados com o SignalR Service, em quaisquer linguagens de programação compatíveis com REST.