---
title: Resolução de problemas Serviço DB da Azure Cosmos indisponível exceção
description: Como diagnosticar e corrigir o serviço Cosmos DB exceção indisponível
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294522"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnosticar e resolver problemas Serviço DB Azure Cosmos indisponível
O SDK não foi capaz de ligar ao serviço Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para exceções indisponíveis do serviço.

### <a name="1-the-required-ports-are-not-enabled"></a>1. As portas necessárias não estão ativadas.
Verifique se todas as [portas necessárias](performance-tips-dotnet-sdk-v3-sql.md#networking) estão ativadas.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Se uma aplicação ou serviço existente começar a receber 503

### <a name="1-there-is-an-outage"></a>1. Há uma paragem
Verifique o [Estado de Azure](https://status.azure.com/status) para ver se há algum problema em curso.

### <a name="2-snat-port-exhaustion"></a>2. Exaustão do Porto SNAT
Siga o [guia de exaustão do porto SNAT](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. As portas necessárias estão a ser bloqueadas
Verifique se todas as [portas necessárias](performance-tips-dotnet-sdk-v3-sql.md#networking) estão ativadas.

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) ao utilizar a Azure Cosmos DB .NET SDK
* Conheça as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)