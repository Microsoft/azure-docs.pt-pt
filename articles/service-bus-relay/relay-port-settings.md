---
title: Definições da porta Azure Relay / Microsoft Docs
description: Este artigo inclui uma tabela que descreve a configuração necessária para os valores da porta para o Relé Azure.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 55bb6b620c7ba9ee13068213cbc867b165acc185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514889"
---
# <a name="azure-relay-port-settings"></a>Definições da porta de retransmissão azure

A tabela seguinte descreve a configuração necessária para os valores da porta para o Relé Azure.

## <a name="hybrid-connections"></a>Ligações Híbridas

Ligações Híbridas utiliza WebSockets na porta 443 com SSL como mecanismo de transporte subjacente, que utiliza apenas **HTTPS.** 

## <a name="wcf-relays"></a>Reencaminhamentos do WCF
  
|Enlace|Segurança dos Transportes|Porta|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (cliente)|Sim|HTTPS| 
|" |Não|HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (serviço)|Qualquer um|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (cliente)|Sim|9351/HTTPS|  
|" |Não|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (serviço)|Qualquer um|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (cliente/serviço)|Qualquer um|5671/9352/HTTP (9352/9353 se utilizar híbrido)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (cliente)|Sim|9351/HTTPS|  
|" |Não|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (serviço)|Qualquer um|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (cliente)|Sim|HTTPS|  
|" |Não|HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (serviço)|Qualquer um|9351/HTTP|  
|[WS2007HttpRelayClasse de Ligação](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (cliente)|Sim|HTTPS|  
|" |Não|HTTP|  
|[WS2007HttpRelayClasse de Ligação](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (serviço)|Qualquer um|9351/HTTP|

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Azure Relay, visite estes links:
* [O que é Azure Relay?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)