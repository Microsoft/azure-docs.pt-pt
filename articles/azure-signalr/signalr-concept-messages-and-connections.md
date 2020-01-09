---
title: Mensagens e conexões no serviço de Signaler do Azure
description: Uma visão geral dos principais conceitos sobre mensagens e conexões no serviço de Signaler do Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392814"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Mensagens e conexões no serviço de Signaler do Azure

O modelo de cobrança do serviço de Signaler do Azure é baseado no número de conexões e no número de mensagens. Este artigo explica como as mensagens e conexões são definidas e contadas para cobrança.


## <a name="message-formats"></a>Formatos de mensagem 

O serviço de Signaler do Azure dá suporte aos mesmos formatos que ASP.NET Core Signalr: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Tamanho da mensagem

O serviço de sinalizador do Azure não tem limite de tamanho para mensagens.

Mensagens grandes são divididas em mensagens menores que não são mais do que 2 KB cada e transmitidas separadamente. Os SDKs lidam com a divisão e a montagem de mensagens. Nenhum esforço de desenvolvedor é necessário.

As mensagens grandes afetam negativamente o desempenho do sistema de mensagens. Use mensagens menores sempre que possível e teste para determinar o tamanho de mensagem ideal para cada cenário de caso de uso.

## <a name="how-messages-are-counted-for-billing"></a>Como as mensagens são contadas para cobrança

Para cobrança, somente as mensagens de saída do serviço de Signaler do Azure são contadas. As mensagens de ping entre clientes e servidores são ignoradas.

Mensagens maiores que 2 KB são contadas como várias mensagens de 2 KB cada. O gráfico de contagem de mensagens na portal do Azure é atualizado a cada 100 mensagens por Hub.

Por exemplo, imagine que você tenha três clientes e um servidor de aplicativos. Um cliente envia uma mensagem de 4 KB para permitir que o servidor seja transmitido a todos os clientes. A contagem de mensagens é oito: uma mensagem do serviço para o servidor de aplicativos e três mensagens do serviço para os clientes. Cada mensagem é contada como duas mensagens de 2 KB.

## <a name="how-connections-are-counted"></a>Como as conexões são contadas

Há conexões de servidor e conexões de cliente com o serviço de Signaler do Azure. Por padrão, cada servidor de aplicativos começa com cinco conexões iniciais por Hub e cada cliente tem uma conexão de cliente.

A contagem de conexões mostrada no portal do Azure inclui conexões de servidor e conexões de cliente.

Por exemplo, suponha que você tenha dois servidores de aplicativos e que defina cinco hubs no código. A contagem de conexões do servidor será 50:2 servidores de aplicativos * 5 hubs * 5 conexões por Hub.

O Signalr ASP.NET calcula as conexões de servidor de forma diferente. Ele inclui um hub padrão além dos hubs que você define. Por padrão, cada servidor de aplicativos precisa de mais cinco conexões de servidor iniciais. A contagem de conexões inicial para o Hub padrão permanece consistente com o dos outros hubs.

Durante o tempo de vida do servidor de aplicativos, o serviço e o servidor de aplicativos mantêm o status da conexão de sincronização e fazem ajustes nas conexões do servidor para melhorar o desempenho e a estabilidade do serviço. Portanto, você pode ver o número de conexão do servidor ser alterado de tempos em tempos.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Como o tráfego de entrada/saída é contado

A distinção entre o tráfego de entrada e o tráfego de saída é baseada na perspectiva do serviço de Signaler do Azure. O tráfego é calculado em bytes.

## <a name="related-resources"></a>Recursos relacionados

- [Tipos de agregação no Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configuração do Signalr ASP.NET Core](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
