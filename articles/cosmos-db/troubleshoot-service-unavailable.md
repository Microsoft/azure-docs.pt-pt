---
title: Resolução de problemas Serviço DB da Azure Cosmos indisponível exceções
description: Saiba como diagnosticar e corrigir o serviço DB da Azure Cosmos indisponível.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870872"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnosticar e resolver problemas Serviço DB Azure Cosmos indisponíveis exceções
O SDK não foi capaz de ligar-se ao Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para exceções indisponíveis do serviço.

### <a name="the-required-ports-are-being-blocked"></a>As portas necessárias estão a ser bloqueadas
Verifique se todas as [portas necessárias](performance-tips-dotnet-sdk-v3-sql.md#networking) estão ativadas.

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