---
title: Armazenamento de cultivo automático - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode permitir o armazenamento de crescimento automático utilizando o Azure CLI na Base de Dados Azure para MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: jroth
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ba997038842a1028b8be5a542adac60186a30cb0
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664440"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Base de dados Azure de crescimento automático para armazenamento MariaDB utilizando o Azure CLI
Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor MariaDB crescer sem afetar a carga de trabalho.

O servidor [que atinge o limite de armazenamento,](concepts-pricing-tiers.md#reaching-the-storage-limit)está definido apenas para leitura. Se o crescimento automático de armazenamento estiver ativado então para servidores com armazenamento com menos de 100 GB de armazenamento a fortado, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito for inferior a 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento especificados [aqui](concepts-pricing-tiers.md#storage) aplicam-se.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia:

- Precisa de uma [base de dados Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="enable-mariadb-server-storage-auto-grow"></a>Ativar o armazenamento do servidor MariaDB a crescer automaticamente

Ativar o armazenamento automático do servidor num servidor existente com o seguinte comando:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Ativar o armazenamento de crescimento automático do servidor enquanto cria um novo servidor com o seguinte comando:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-metric.md)
