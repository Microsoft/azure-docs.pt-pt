---
title: Mensagens e as ligações no serviço Azure SignalR
description: Uma visão geral dos conceitos chave sobre as mensagens e as ligações no serviço Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e82ce8f5c97aed7e2cb832d8e808ff84691f7c9e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555304"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Mensagens e as ligações no serviço Azure SignalR

O modelo de faturação para o serviço Azure SignalR baseia-se no número de ligações e o número de mensagens. Este artigo explica como ligações e as mensagens são definidas e contabilizadas para faturação.


## <a name="message-formats"></a>Formatos de mensagem 

Serviço Azure SignalR suporta os formatos mesmo como SignalR do ASP.NET Core: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Tamanho da mensagem

Serviço Azure SignalR tem sem limite de tamanho para mensagens.

Mensagens grandes são divididas em mensagens menores que são mais do que 2 KB cada e transmitidas em separado. Mensagem de identificador de SDKs divisão e montagem. São necessários sem esforços de desenvolvimento.

Mensagens grandes afetar negativamente o desempenho de mensagens. Utilize mensagens menores, sempre que possível e teste para determinar o tamanho de mensagem ideal para cada cenário de casos de utilização.

## <a name="how-messages-are-counted-for-billing"></a>Como as mensagens são contabilizadas para faturação

Para faturação, apenas mensagens de saída do serviço Azure SignalR são contabilizadas. Mensagens de ping entre clientes e servidores são ignoradas.

Mensagens superiores a 2 KB são contadas como mensagens de vários de 2 KB. O gráfico de contagem de mensagens no portal do Azure é atualizado cada 100 mensagens por hub.

Por exemplo, imagine que tem três clientes e um servidor de aplicativos. Um cliente envia uma mensagem de 4 KB para permitir que o servidor de difusão para todos os clientes. A contagem de mensagens é oito: uma mensagem a partir do serviço para o servidor de aplicações e o três de mensagens do serviço para os clientes. Cada mensagem é contabilizada como duas mensagens de 2 KB.

A contagem de mensagens apresentada no portal do Azure irá permanecer 0, até que ele acumula para ser mais do que 100.

## <a name="how-connections-are-counted"></a>Como as ligações são contadas

Existem ligações de servidor e ligações de cliente. Por predefinição, cada servidor de aplicativo tem cinco ligações por hub com o serviço Azure SignalR e cada cliente tem uma ligação de cliente com o serviço Azure SignalR.

A contagem de ligação, mostrada no portal do Azure inclui ligações de servidor e ligações de cliente.

Por exemplo, suponha que tenha dois servidores de aplicações e que define cinco hubs no código. A contagem de ligação do servidor será 50: 2 servidores de aplicações * 5 hubs * 5 ligações por hub.

O SignalR de ASP.NET calcula ligações de servidor, de forma diferente. Ele inclui um hub de padrão, além de hubs por si. Por predefinição, cada servidor de aplicativos tem cinco mais ligações de servidor. A contagem de ligação para o hub de predefinição permanece consistente com que os outros hubs.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Como o tráfego de entrada/saída é contabilizado

A distinção entre o tráfego de entrada e o tráfego de saída baseia-se a perspectiva do serviço Azure SignalR. O tráfego é calculado em bytes. Como a contagem de mensagens, o tráfego também tem uma taxa de amostragem. O gráfico de entrada/saída no portal do Azure é atualizado cada 100 KB por hub.

## <a name="related-resources"></a>Recursos relacionados

- [Tipos de agregação no Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configuração do ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)