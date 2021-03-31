---
title: Armazenamento de crescimento automático - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode configurar o armazenamento de crescimento automático utilizando o Azure CLI na Base de Dados Azure para PostgreSQL - Servidor Único.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b6f3875af4c7f0eace327d810c632a6bb217092
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534199"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Base de dados Azure de crescimento automático para armazenamento pós-greSQL - Servidor Único usando o Azure CLI
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento de servidores PostgreSQL crescer sem afetar a carga de trabalho.

O servidor [que atinge o limite de armazenamento,](./concepts-pricing-tiers.md#reaching-the-storage-limit)está definido apenas para leitura. Se o crescimento automático de armazenamento estiver ativado então para servidores com armazenamento com menos de 100 GB de armazenamento a fortado, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito for inferior a 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento especificados [aqui](./concepts-pricing-tiers.md#storage) aplicam-se.

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma [base de dados Azure para o servidor PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="enable-postgresql-server-storage-auto-grow"></a>Ativar o armazenamento do servidor PostgreSQL

Ativar o armazenamento automático do servidor num servidor existente com o seguinte comando:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Ativar o armazenamento de crescimento automático do servidor enquanto cria um novo servidor com o seguinte comando:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)