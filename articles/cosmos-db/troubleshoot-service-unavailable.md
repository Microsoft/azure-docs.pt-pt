---
title: Resolução de problemas Serviço DB da Azure Cosmos indisponível exceção
description: Como diagnosticar e corrigir o serviço Cosmos DB exceção indisponível
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987380"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnosticar e resolver problemas Serviço DB Azure Cosmos indisponível
O SDK não foi capaz de ligar ao serviço Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para exceções indisponíveis do serviço.

### <a name="1-the-required-ports-are-being-blocked"></a>1. As portas necessárias estão a ser bloqueadas
Verifique se todas as [portas necessárias](performance-tips-dotnet-sdk-v3-sql.md#networking) estão ativadas.

### <a name="2-client-side-transient-connectivity-issues"></a>2. Problemas de conectividade transitória do lado do cliente
As exceções indisponíveis do serviço podem surgir quando existem problemas transitórios de conectividade que estão a causar intervalos de tempo. Normalmente, os vestígios da pilha relacionados com este cenário conterão `TransportException` um, por exemplo:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Acompanhe o tempo limite de [tempo de pedido](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) para resolver o problema.

### <a name="3-service-outage"></a>3. Paragem de serviço
Verifique o [Estado de Azure](https://status.azure.com/status) para ver se há algum problema em curso.


## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) ao utilizar a Azure Cosmos DB .NET SDK
* Conheça as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)