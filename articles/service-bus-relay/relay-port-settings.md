---
title: Definições de porta de reencaminhamento do Azure | Documentos da Microsoft
description: Detalhes sobre os valores de portas de reencaminhamento do Azure.
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
ms.date: 06/26/2018
ms.author: spelluru
ms.openlocfilehash: 3ef08cfc94a029f97250578e9b0366a18770c809
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696748"
---
# <a name="azure-relay-port-settings"></a>Definições de porta de reencaminhamento do Azure

A tabela seguinte descreve a configuração necessária para os valores de porta para o reencaminhamento do Azure.

## <a name="hybrid-connections"></a>Ligações Híbridas

Ligações híbridas utiliza WebSockets na porta 443 com SSL, como o mecanismo de transporte subjacente, que usa **HTTPS** apenas. 

## <a name="wcf-relays"></a>Reencaminhamentos do WCF
  
|Vínculo|Segurança de transporte|Porta|  
|-------------|------------------------|----------|  
|[Classe de BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (cliente)|Sim|HTTPS| 
|" |Não|HTTP|  
|[Classe de BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (serviço)|Qualquer um dos|9351/HTTP|  
|[Classe de NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (cliente)|Sim|9351/HTTPS|  
|" |Não|9350/HTTP|  
|[Classe de NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (serviço)|Qualquer um dos|9351/HTTP|  
|[Classe de NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (cliente/serviço)|Qualquer um dos|5671/9352/HTTP (9352/9353 se utilizar híbrida)|  
|[Classe de NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (cliente)|Sim|9351/HTTPS|  
|" |Não|9350/HTTP|  
|[Classe de NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (serviço)|Qualquer um dos|9351/HTTP|  
|[Classe de WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (cliente)|Sim|HTTPS|  
|" |Não|HTTP|  
|[Classe de WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (serviço)|Qualquer um dos|9351/HTTP|  
|[Classe de WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (cliente)|Sim|HTTPS|  
|" |Não|HTTP|  
|[Classe de WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (serviço)|Qualquer um dos|9351/HTTP|

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o reencaminhamento do Azure, visite estas ligações:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)