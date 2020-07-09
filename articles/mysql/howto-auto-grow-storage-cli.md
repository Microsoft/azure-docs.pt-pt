---
title: Armazenamento de cultivo automático - Azure CLI - Base de Dados Azure para MySQL
description: Este artigo descreve como pode permitir o armazenamento de crescimento automático usando o Azure CLI na Base de Dados Azure para o MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: fd7b9a809421aa33b83902960da2f02d4deabf9a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113269"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Base de dados Azure de crescimento automático para armazenamento MySQL utilizando o Azure CLI
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor MySQL crescer sem afetar a carga de trabalho.

O servidor [que atinge o limite de armazenamento,](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)está definido apenas para leitura. Se o crescimento automático de armazenamento estiver ativado então para servidores com armazenamento com menos de 100 GB de armazenamento a fortado, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito for inferior a 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento especificados [aqui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) aplicam-se.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no pedido de comando do Azure CLI, insira `az --version` . Para instalar ou atualizar, consulte [instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Ativar o armazenamento do servidor MySQL a crescer automaticamente

Ativar o armazenamento automático do servidor num servidor existente com o seguinte comando:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Ativar o armazenamento de crescimento automático do servidor enquanto cria um novo servidor com o seguinte comando:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Próximos passos

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)