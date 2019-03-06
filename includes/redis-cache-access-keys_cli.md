---
title: incluir ficheiro
description: incluir ficheiro
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 778151e401624398b70101a242e4cf0be8e0a1b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443694"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Obtenha o nome do anfitrião, as portas e as chaves de acesso através da CLI do Azure

Para obter o nome de anfitrião e as portas com a CLI do Azure, pode chamar [show do az redis](https://docs.microsoft.com/cli/azure/redis#az_redis_show)e para obter as chaves pode chamar [az redis lista as chaves](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). O script seguinte chama estes dois comandos e ecoa o nome de anfitrião, portas e as chaves para a consola.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Para obter mais informações sobre este script, consulte [obter o nome de anfitrião, portas e as chaves para a Cache do Azure para Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). Para obter mais informações sobre a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
