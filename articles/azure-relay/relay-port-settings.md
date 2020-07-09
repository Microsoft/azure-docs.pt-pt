---
title: Definições da porta Azure Relay / Microsoft Docs
description: Este artigo inclui uma tabela que descreve a configuração necessária para os valores da porta para O Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 97640debe81041ff7e2b082c6a9ac606d6088664
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314272"
---
# <a name="azure-relay-port-settings"></a>Definições da porta Azure Relay

A tabela seguinte descreve a configuração necessária para os valores da porta para O Relé Azure.

## <a name="hybrid-connections"></a>Ligações Híbridas

A Hybrid Connections utiliza webSockets na porta 443 com TLS como o mecanismo de transporte subjacente, que utiliza **apenas HTTPS.** 

## <a name="wcf-relays"></a>Reencaminhamentos do WCF
  
|Enlace|Segurança dos Transportes|Porta|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (cliente)|Sim|HTTPS| 
|" |Não|HTTP|  
|[BasicHttpRelayBinding Class](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (serviço)|Qualquer um|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (cliente)|Sim|9351/HTTPS|  
|" |Não|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (serviço)|Qualquer um|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (cliente/serviço)|Qualquer um|5671/9352/HTTP (9352/9353 se utilizar híbrido)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (cliente)|Sim|9351/HTTPS|  
|" |Não|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (serviço)|Qualquer um|9351/HTTP|  
|[Aula webHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (cliente)|Sim|HTTPS|  
|" |Não|HTTP|  
|[Aula webHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (serviço)|Qualquer um|9351/HTTP|  
|[WS2007HttpRelayBinding Class](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (cliente)|Sim|HTTPS|  
|" |Não|HTTP|  
|[WS2007HttpRelayBinding Class](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (serviço)|Qualquer um|9351/HTTP|

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre o Azure Relay, visite estes links:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)