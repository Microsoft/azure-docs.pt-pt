---
title: O que é o serviço de Signaler do Azure?
description: Uma visão geral do serviço de sinalizador do Azure.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 06/20/2019
ms.author: zhshang
ms.openlocfilehash: e7bdc62f7fa46bbacce7f264d8f331ea64b05430
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273696"
---
# <a name="what-is-azure-signalr-service"></a>O que é o serviço de Signaler do Azure?

O Azure SignalR Service simplifica o processo de acrescentar a funcionalidade Web de tempo real a aplicações através de HTTP. Esta funcionalidade em tempo real permite que o serviço envie atualizações de conteúdo a clientes ligados, como uma única página Web ou aplicação móvel. Como resultado, os clientes são atualizados sem que tenham de consultar o servidor ou submeter novos pedidos HTTP para as atualizações.


Este artigo disponibiliza uma descrição geral do Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Para que serve o Azure SignalR Service?

Qualquer cenário que exija o envio de dados do servidor para o cliente em tempo real pode usar o serviço de Signaler do Azure.

Os recursos tradicionais em tempo real que geralmente exigem a sondagem do servidor também podem usar o serviço de Signaler do Azure.

O serviço de Signaler do Azure foi usado em uma ampla variedade de setores, para qualquer tipo de aplicativo que exija atualizações de conteúdo em tempo real. Listamos alguns exemplos que são bons para usar o serviço do Azure Signalr:

* **Atualizações de dados de alta frequência:** jogos, votação, sondagem, leilão.
* **Painéis e monitoramento:** painel da empresa, dados de mercado financeiro, atualização de vendas instantâneas, quadro líder de jogos de vários jogadores e monitoramento de IOT.
* **Chat:** sala de chat ao vivo, bot de chat, suporte ao cliente online, assistente de compras em tempo real, Messenger, bate-papo em jogo e assim por diante.
* **Local em tempo real no mapa:** controle de logística, acompanhamento de status de entrega, atualizações de status de transporte, aplicativos de GPS.
* **Anúncios direcionados em tempo real:** anúncios e ofertas de push em tempo real personalizados, anúncios interativos.
* **Aplicativos de colaboração:** coautoria, aplicativos de quadro de comunicações e software de reunião de equipe.
* **Notificações por push:** rede social, email, jogo, alerta de viagem.
* **Transmissão em tempo real:** transmissão de vídeo/áudio ao vivo, legenda dinâmica, tradução, eventos/difusão de notícias.
* **Dispositivos de IOT e conectados:** métricas de IOT em tempo real, controle remoto, status em tempo real e rastreamento de local.
* **Automação:** gatilho em tempo real de eventos upstream.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Quais são os benefícios que usam o serviço do Azure Signalr?

**Baseado em padrão:**

O SignalR oferece uma abstração através de um número de técnicas utilizadas para compilar aplicações Web em tempo real. [WebSockets](https://wikipedia.org/wiki/WebSocket) é o transporte ideal, mas podem ser utilizadas outras técnicas como [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) e Long Polling, se não estiverem disponíveis outras opções. O SignalR deteta e inicializa automaticamente o transporte adequado com base nas funcionalidades que o servidor e o cliente suportam.

**Suporte nativo a ASP.NET Core:**

O serviço signalr fornece experiência de programação nativa com ASP.NET Core e ASP.NET. O desenvolvimento de um novo aplicativo Signalr com o serviço Signalr ou a migração de um aplicativo com base no Signalr existente para o serviço Signalr requer esforços mínimos.
O serviço signalr também dá suporte ao novo recurso do ASP.NET Core, mais bem no servidor.

**Amplo suporte a clientes:**

O serviço signalr funciona com uma ampla variedade de clientes, como navegadores Web e móveis, aplicativos de desktop, aplicativos móveis, processo de servidor, dispositivos IoT e consoles de jogos. O serviço signalr oferece SDKs em diferentes idiomas. Além dos SDKs nativos ASP.NET Core ou ASP.NET C# , o serviço signalr também fornece o SDK de cliente JavaScript, para habilitar clientes Web e muitas estruturas JavaScript. O SDK do cliente Java também tem suporte para aplicativos Java, incluindo aplicativos nativos do Android. O serviço signalr dá suporte à API REST e sem servidor por meio de integrações com Azure Functions e a grade de eventos.

**Lidar com conexões de cliente em larga escala:**

O serviço signalr foi projetado para aplicativos em tempo real em grande escala. O serviço signalr permite que várias instâncias trabalhem em conjunto para serem dimensionadas para milhões de conexões de clientes. O serviço também dá suporte a várias regiões globais para fins de fragmentação, alta disponibilidade ou recuperação de desastre.

**Remova a carga para o Signalr de autoatendimento:**

Em comparação com os aplicativos de Signaler autohospedados, alternar para o serviço de sinalização removerá a necessidade de gerenciar planos de fundo que lidam com as conexões de escala e cliente. O serviço totalmente gerenciado também simplifica os aplicativos Web e economiza o custo de hospedagem. O serviço signalr oferece um alcance global e data center e rede de nível mundial, escala para milhões de conexões, garante SLA e, ao mesmo tempo, fornece toda a conformidade e segurança no Azure Standard.

![Serviço de Signaler gerenciado](./media/signalr-overview/managed-signalr-service.png)

**Ofereça APIs avançadas para diferentes padrões de mensagens:**

O serviço signalr permite que o servidor envie mensagens a uma conexão específica, a todas as conexões ou a um subconjunto de conexões que pertencem a um usuário específico ou que foram colocadas em um grupo arbitrário.

## <a name="how-to-use-azure-signalr-service"></a>Como utilizar o Azure SignalR Service

Há várias maneiras diferentes de programar com o serviço de Signaler do Azure, como alguns dos exemplos listados aqui:

- **[Dimensionar uma Aplicação do SignalR do ASP.NET Core](signalr-concept-scale-aspnet-core.md)** - Integrar o Azure SignalR Service numa aplicação do SignalR do ASP.NET Core para aumentar horizontalmente para centenas de milhares de ligações.
- **[Criar aplicações em tempo real sem servidor](signalr-concept-azure-functions.md)** - Utilize a integração das Funções do Azure com o Azure SignalR Service para criar aplicações em tempo real sem servidor em linguagens como JavaScript, C# e Java.
- **[Enviar mensagens de servidor a clientes através da API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** - O Azure SignalR Service apresenta a API REST para permitir que as aplicações publiquem mensagens para clientes ligados com o SignalR Service, em quaisquer linguagens de programação compatíveis com REST.
