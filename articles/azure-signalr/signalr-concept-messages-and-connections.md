---
title: Mensagens e ligações no Serviço Azure SignalR
description: Uma visão geral dos conceitos-chave sobre mensagens e conexões no Serviço Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75392814"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Mensagens e ligações no Serviço Azure SignalR

O modelo de faturação do Serviço Azure SignalR baseia-se no número de ligações e no número de mensagens. Este artigo explica como as mensagens e ligações são definidas e contadas para faturação.


## <a name="message-formats"></a>Formatos de mensagens 

O Serviço Azure SignalR suporta os mesmos formatos que ASP.NET Core SignalR: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Tamanho da mensagem

O Serviço Azure SignalR não tem limite de tamanho para mensagens.

As mensagens grandes são divididas em mensagens menores que não são mais do que 2 KB cada e transmitidas separadamente. Os SDKs lidam com a divisão e montagem de mensagens. Não são necessários esforços de desenvolvimento.

As mensagens grandes afetam negativamente o desempenho das mensagens. Utilize mensagens mais pequenas sempre que possível e teste para determinar o tamanho ideal da mensagem para cada cenário de utilização.

## <a name="how-messages-are-counted-for-billing"></a>Como as mensagens são contadas para faturação

Para a faturação, apenas são contadas mensagens de saída do Serviço Azure SignalR. As mensagens de ping entre clientes e servidores são ignoradas.

As mensagens maiores do que 2 KB são contadas como múltiplas mensagens de 2 KB cada. O gráfico de contagem de mensagens no portal Azure é atualizado a cada 100 mensagens por hub.

Por exemplo, imagine que tem três clientes e um servidor de aplicações. Um cliente envia uma mensagem 4-KB para permitir que o servidor transmita a todos os clientes. A contagem de mensagens é de oito: uma mensagem do serviço para o servidor de aplicações e três mensagens do serviço para os clientes. Cada mensagem é contada como duas mensagens 2-KB.

## <a name="how-connections-are-counted"></a>Como as ligações são contadas

Existem ligações ao servidor e ligações ao cliente com o Serviço Azure SignalR. Por predefinição, cada servidor de aplicação começa com cinco ligações iniciais por hub, e cada cliente tem uma ligação com o cliente.

A contagem de ligação mostrada no portal Azure inclui ligações ao servidor e ligações ao cliente.

Por exemplo, assuma que tem dois servidores de aplicações e que define cinco hubs em código. A contagem de ligação do servidor será de 50: 2 servidores de aplicações * 5 hubs * 5 ligações por hub.

ASP.NET SignalR calcula as ligações do servidor de uma forma diferente. Inclui um hub padrão para além dos centros que define. Por predefinição, cada servidor de aplicação necessita de mais cinco ligações iniciais do servidor. A contagem inicial de ligação para o hub padrão permanece consistente com a dos outros centros.

Durante o período de vida útil do servidor de aplicações, o serviço e o servidor de aplicações mantêm o estado de ligação sincronizada e fazem ajustes nas ligações do servidor para um melhor desempenho e estabilidade do serviço. Assim, pode ver alterações no número de ligação do servidor de vez em quando.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Como o tráfego de entrada/saída é contado

A distinção entre tráfego de entrada e tráfego de saída baseia-se na perspetiva do Serviço Azure SignalR. O tráfego é calculado em bytes.

## <a name="related-resources"></a>Recursos relacionados

- [Tipos de agregação no Monitor Azure](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET configuração do Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [Pacote de Mensagens](/aspnet/core/signalr/messagepackhubprotocol)
