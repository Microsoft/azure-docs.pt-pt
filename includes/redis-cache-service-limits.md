---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839236"
---
| Recurso | Limite |
| --- | --- |
| Tamanho da cache |1.2 TB |
| Bases de Dados |64 |
| Máximo de clientes conectados |40,000 |
| Cache do Azure para réplicas Redis, para alta disponibilidade |1 |
| Partições horizontais numa cache premium com clustering |10 |

Limita a Cache de Redis do Azure e os tamanhos são diferentes para cada escalão de preço. Para ver os tipos de preço e seus tamanhos associados, veja [preços do cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/).

Para obter mais informações na Cache do Azure para limites de configuração do Redis, consulte [configuração do servidor predefinido Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Porque a configuração e gestão de Cache do Azure para instâncias de Redis é feito pela Microsoft, nem todos os comandos da Redis são suportados na Cache do Azure para Redis. Para obter mais informações, consulte [Redis comandos não suportados na Cache do Azure para Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

