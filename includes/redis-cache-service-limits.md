---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184663"
---
| Recurso | Limite |
| --- | --- |
| Tamanho da cache |530 GB |
| Bases de Dados |64 |
| Máximo de clientes conectados |40,000 |
| Cache do Azure para réplicas Redis, para alta disponibilidade |1 |
| Partições horizontais numa cache premium com clustering |10 |

Limita a Cache de Redis do Azure e os tamanhos são diferentes para cada escalão de preço. Para ver os tipos de preço e seus tamanhos associados, veja [preços do cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/).

Para obter mais informações na Cache do Azure para limites de configuração do Redis, consulte [configuração do servidor predefinido Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Porque a configuração e gestão de Cache do Azure para instâncias de Redis é feito pela Microsoft, nem todos os comandos da Redis são suportados na Cache do Azure para Redis. Para obter mais informações, consulte [Redis comandos não suportados na Cache do Azure para Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

