---
title: As definições da porta Azure Relay | Microsoft Docs
description: Este artigo inclui uma tabela que descreve a configuração necessária para os valores da porta para O Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 81551055d967babaac6f12c3a23ce6b1e78afbd5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314172"
---
# <a name="azure-relay-port-settings"></a>Definições da porta Azure Relay

A tabela seguinte descreve a configuração necessária para os valores da porta para O Relé Azure.

## <a name="hybrid-connections"></a>Ligações Híbridas

A Hybrid Connections utiliza webSockets na porta 443 com TLS como o mecanismo de transporte subjacente, que utiliza **apenas HTTPS.** 

## <a name="wcf-relays"></a>Reencaminhamentos do WCF
  
|Enlace|Segurança dos Transportes|Porta|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (cliente)|Yes|HTTPS| 
|" |No|HTTP|  
|[BasicHttpRelayBinding Class](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (serviço)|Ambas|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (cliente)|Yes|9351/HTTPS|  
|" |No|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (serviço)|Ambas|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (cliente/serviço)|Ambas|5671/9352/HTTP (9352/9353 se utilizar híbrido)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (cliente)|Yes|9351/HTTPS|  
|" |No|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (serviço)|Ambas|9351/HTTP|  
|[Aula webHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (cliente)|Yes|HTTPS|  
|" |No|HTTP|  
|[Aula webHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (serviço)|Ambas|9351/HTTP|  
|[WS2007HttpRelayBinding Class](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (cliente)|Yes|HTTPS|  
|" |No|HTTP|  
|[WS2007HttpRelayBinding Class](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (serviço)|Ambas|9351/HTTP|

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Azure Relay, visite estes links:
* [O que é o Azure Relay?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.yml)