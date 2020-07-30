---
title: Gerir a Cache Azure para Redis com Azure CLI
description: 'Amostras de Azure CLI para gerir Azure Cache para Redis: Criar uma cache, eliminar uma cache, obter detalhes de cache, nome de anfitrião, portas e chaves, conectando uma aplicação web.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: 345fd0272e0cfe9b4d7329f004e628ea7820c2d9
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421245"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Gerir a Cache Azure para Redis com Azure CLI

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| Criar cache | Descrição |
| ------------ | ----------- |
| [Criar uma cache](./scripts/create-cache.md) | Cria um grupo de recursos e um cache Azure de nível básico para redis. |
| [Criar uma cache premium com clustering](./scripts/create-premium-cache-cluster.md) | Cria um grupo de recursos e uma cache de nível premium com clustering ativado.|
| [Obtenha detalhes da cache](./scripts/show-cache.md) | Obtém detalhes de uma Cache Azure para a instância Redis, incluindo o estado de provisionamento. |
| [Pegue o nome de anfitrião, portas e chaves](./scripts/cache-keys-ports.md) | Obtém o nome de anfitrião, portas e chaves para um Azure Cache para a instância Redis. |
|**Web app mais cache**| **Descrição**|
| [Conecte uma aplicação web a um Azure Cache para Redis](./../app-service/scripts/cli-connect-to-redis.md) | Cria uma aplicação web Azure e um Cache Azure para Redis, em seguida, adiciona os detalhes da ligação redis às definições da aplicação. |
|**Eliminar cache**| **Descrição** |
| [Apagar uma cache](./scripts/delete-cache.md) | Elimina uma Cache Azure para a instância Redis  |

Para obter mais informações sobre o Azure CLI, consulte [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e [começar com o Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
