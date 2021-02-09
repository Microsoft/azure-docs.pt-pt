---
title: Resolução de problemas Azure Cosmos DB proibiu exceções
description: Saiba como diagnosticar e corrigir exceções proibidas.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99971913"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnosticar e resolver problemas Azure Cosmos DB exceções proibidas
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O código de estado HTTP 403 representa o pedido é proibido de ser preenchido.

## <a name="firewall-blocking-requests"></a>Pedidos de bloqueio de firewall
Neste cenário, é comum ver erros como os abaixo:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Solução
Verifique se as definições de [firewall atuais](how-to-configure-firewall.md) estão corretas e inclua os IPs ou redes a partir do quais está a tentar ligar.
Se os atualizar recentemente, lembre-se de que as alterações podem demorar **até 15 minutos a ser aplicadas**.

## <a name="next-steps"></a>Passos seguintes
* Configurar a [firewall IP](how-to-configure-firewall.md).
* Configure o acesso a partir de [redes virtuais.](how-to-configure-vnet-service-endpoint.md)
* Configure o acesso a partir de [pontos finais privados](how-to-configure-private-endpoints.md).
