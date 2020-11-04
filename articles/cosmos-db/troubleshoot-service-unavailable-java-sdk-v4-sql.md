---
title: Resolução de problemas Serviço DB da Azure Cosmos indisponível com o Java v4 SDK
description: Saiba como diagnosticar e corrigir o serviço DB Azure Cosmos indisponível com o Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340095"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnosticar e resolver problemas Serviço Azure Cosmos DB Java v4 SDK indisponíveis exceções
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
O Java v4 SDK não foi capaz de ligar-se ao Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para exceções indisponíveis do serviço.

### <a name="the-required-ports-are-being-blocked"></a>As portas necessárias estão a ser bloqueadas
Verifique se todas as [portas necessárias](sql-sdk-connection-modes.md#service-port-ranges) estão ativadas.

### <a name="client-side-transient-connectivity-issues"></a>Problemas de conectividade transitória do lado do cliente
As exceções indisponíveis do serviço podem surgir quando existem problemas transitórios de conectividade que estão a causar intervalos de tempo. Normalmente, o traço de pilha relacionado com este cenário conterá um `ServiceUnavailableException` erro com detalhes de diagnóstico. Por exemplo:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Siga as [medidas de resolução de problemas](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) do pedido para resolvê-lo.

### <a name="service-outage"></a>Falha do serviço
Verifique o estado do [Azure](https://status.azure.com/status) para ver se há algum problema em curso.


## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-java-sdk-v4-sql.md) quando utilizar o Azure Cosmos DB Java v4 SDK.
* Saiba mais sobre as diretrizes de desempenho para [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).