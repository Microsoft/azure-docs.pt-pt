---
title: Armazenamento de cultivo automático - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode permitir o armazenamento de cultivo automático utilizando o Azure CLI em Base de Dados Azure para MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4be84c750f6a3ca7a0d48aa2b98d75272c1cbadf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79529089"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Base de Dados Azure de cultivo automático para armazenamento MariaDB utilizando o Azure CLI
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor MariaDB crescer sem afetar a carga de trabalho.

O servidor [que atinge o limite](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)de armazenamento está definido para ler apenas. Se o cultivo automático de armazenamento for ativado para servidores com armazenamento amenos de 100 GB, o tamanho de armazenamento previsto é aumentado em 5 GB assim que o armazenamento gratuito for inferior ao maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. [Limites](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) máximos de armazenamento conforme aqui especificado se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no aviso de `az --version`comando Azure CLI, introduza . Para instalar ou atualizar, consulte [Instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>Ativar o armazenamento automático do servidor MariaDB

Ativar o armazenamento de crescimento automático do servidor num servidor existente com o seguinte comando:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Ative o armazenamento de crescimento automático do servidor enquanto cria um novo servidor com o seguinte comando:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-metric.md)