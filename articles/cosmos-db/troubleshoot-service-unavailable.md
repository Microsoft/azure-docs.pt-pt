---
title: Resolução de problemas Serviço DB da Azure Cosmos indisponível exceções
description: Saiba como diagnosticar e corrigir o serviço DB da Azure Cosmos indisponível.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 44dcaa270e167ada22d48d1061c3eb3ed3ac88f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097877"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnosticar e resolver problemas Serviço DB Azure Cosmos indisponíveis exceções
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O SDK não foi capaz de ligar-se ao Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para exceções indisponíveis do serviço.

### <a name="the-required-ports-are-being-blocked"></a>As portas necessárias estão a ser bloqueadas
Verifique se todas as [portas necessárias](sql-sdk-connection-modes.md#service-port-ranges) estão ativadas.

### <a name="client-side-transient-connectivity-issues"></a>Problemas de conectividade transitória do lado do cliente
As exceções indisponíveis do serviço podem surgir quando existem problemas transitórios de conectividade que estão a causar intervalos de tempo. Normalmente, o traço de pilha relacionado com este cenário conterá um `TransportException` erro. Por exemplo:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Siga as [medidas de resolução de problemas](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) do pedido para resolvê-lo.

### <a name="service-outage"></a>Falha do serviço
Verifique o estado do [Azure](https://status.azure.com/status) para ver se há algum problema em curso.


## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).