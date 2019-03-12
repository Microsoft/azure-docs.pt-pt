---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554075"
---
| Recurso | Limite |
| --- | --- |
| Tamanho da cache |530 GB |
| Bases de Dados |64 |
| Máximos de clientes ligados |40,000 |
| Cache do Azure para réplicas de Redis, para elevada disponibilidade |1 |
| Partições horizontais numa cache premium com clustering |10 |

Limita a Cache de Redis do Azure e os tamanhos são diferentes para cada escalão de preço. Para ver os escalões de preço e seus tamanhos associados, consulte [Cache do Azure para Redis preços](https://azure.microsoft.com/pricing/details/cache/).

Para obter mais informações na Cache do Azure para limites de configuração do Redis, consulte [configuração do servidor predefinido Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Porque a configuração e gestão de Cache do Azure para instâncias de Redis é feito pela Microsoft, nem todos os comandos da Redis são suportados na Cache do Azure para Redis. Para obter mais informações, consulte [Redis comandos não suportados na Cache do Azure para Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

