---
title: Mensagens e ligações no Serviço Azure SignalR
description: Uma visão geral dos conceitos-chave sobre mensagens e conexões no Serviço Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491950"
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

Por exemplo, imagine que tem um servidor de aplicações e três clientes:

O servidor de aplicações transmite uma mensagem de 1-KB a todos os clientes conectados, a mensagem do servidor de aplicações para o serviço é considerada mensagem de entrada gratuita. Apenas as três mensagens que enviam do serviço para cada um dos clientes são faturadas como mensagens de saída.

O Cliente A envia uma mensagem de 1-KB para outro cliente B, sem passar pelo servidor de aplicações. A mensagem do cliente A para o serviço é mensagem de entrada gratuita. A mensagem do serviço para o cliente B é faturada como mensagem de saída.

Se tiver três clientes e um servidor de aplicações. Um cliente envia uma mensagem 4-KB para permitir que o servidor transmita a todos os clientes. A contagem de mensagens faturadas é de oito: uma mensagem do serviço para o servidor de aplicações e três mensagens do serviço para os clientes. Cada mensagem é contada como duas mensagens 2-KB.

## <a name="how-connections-are-counted"></a>Como as ligações são contadas

Existem ligações ao servidor e ligações ao cliente com o Serviço Azure SignalR. Por predefinição, cada servidor de aplicação começa com cinco ligações iniciais por hub, e cada cliente tem uma ligação com o cliente.

Por exemplo, assuma que tem dois servidores de aplicações e define cinco hubs em código. A contagem de ligação do servidor será de 50: 2 servidores de aplicações * 5 hubs * 5 ligações por hub.

A contagem de ligação mostrada no portal Azure inclui ligações ao servidor, ligações ao cliente, ligações de diagnóstico e ligações de vestígios ao vivo. Os tipos de ligação são definidos na seguinte lista:

- **Ligação do servidor**: Liga o Serviço Azure SignalR e o servidor de aplicações.
- **Ligação ao cliente**: Liga o Serviço Azure SignalR e a aplicação do cliente.
- **Ligação de diagnóstico**: Um tipo especial de ligação ao cliente que pode produzir um registo mais detalhado, que pode afetar o desempenho. Este tipo de cliente é projetado para resolver problemas.
- **Ligação ao traço ao vivo**: Liga-se ao ponto final do traço vivo e recebe vestígios ao vivo do Serviço Azure SignalR. 
 
Note que uma ligação ao vivo não é contada como uma ligação ao cliente ou como uma ligação de servidor. 

ASP.NET SignalR calcula as ligações do servidor de uma forma diferente. Inclui um hub padrão para além dos centros que define. Por predefinição, cada servidor de aplicação necessita de mais cinco ligações iniciais do servidor. A contagem inicial de ligação para o hub padrão permanece consistente com outros centros.

O serviço e o servidor de aplicações continuam a sincronizar o estado de ligação e a fazer ajustes nas ligações do servidor para obter um melhor desempenho e estabilidade do serviço.  Assim, pode ver alterações no número de ligação do servidor de vez em quando.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Como o tráfego de entrada/saída é contado

A mensagem enviada para o serviço é uma mensagem de entrada. A mensagem enviada para fora do serviço é uma mensagem de saída. O tráfego é calculado em bytes.

## <a name="related-resources"></a>Recursos relacionados

- [Tipos de agregação no Monitor Azure](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [ASP.NET configuração do Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [Pacote de Mensagens](/aspnet/core/signalr/messagepackhubprotocol)
