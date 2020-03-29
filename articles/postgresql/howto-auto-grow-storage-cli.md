---
title: Armazenamento de cultivo automático - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode configurar o armazenamento de modo auto-crescimento utilizando o Azure CLI em Base de Dados Azure para PostgreSQL - Servidor Único.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b0dc2fbb168d9325439ee18a227f71a3b88ef9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74767964"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Base de Dados Azure de cultivo automático para armazenamento PostgreSQL - Servidor Único utilizando o Azure CLI
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor PostgreSQL para crescer sem afetar a carga de trabalho.

O servidor [que atinge o limite](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)de armazenamento está definido para ler apenas. Se o cultivo automático de armazenamento for ativado para servidores com armazenamento amenos de 100 GB, o tamanho de armazenamento previsto é aumentado em 5 GB assim que o armazenamento gratuito for inferior ao maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. [Limites](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) máximos de armazenamento conforme aqui especificado se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no aviso de `az --version`comando Azure CLI, introduza . Para instalar ou atualizar, consulte [Instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-postgresql-server-storage-auto-grow"></a>Ativar o armazenamento automático do servidor PostgreSQL

Ativar o armazenamento de crescimento automático do servidor num servidor existente com o seguinte comando:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Ative o armazenamento de crescimento automático do servidor enquanto cria um novo servidor com o seguinte comando:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)