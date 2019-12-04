---
title: Aumento automático do armazenamento-CLI do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como você pode habilitar o armazenamento de crescimento automático usando o CLI do Azure no banco de dados do Azure para MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fb39c3afa2977038df7c9a233ba6720771f545dc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770633"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Aumento automático do armazenamento do banco de dados do Azure para MySQL usando o CLI do Azure
Este artigo descreve como você pode configurar um armazenamento de banco de dados do Azure para servidor MySQL para crescer sem afetar a carga de trabalho.

O servidor que está [alcançando o limite de armazenamento](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)está definido como somente leitura. Se o crescimento automático do armazenamento estiver habilitado, para servidores com menos de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado será aumentado em 5 GB assim que o armazenamento livre estiver abaixo do maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 5% do tamanho de armazenamento provisionado. Os limites de armazenamento máximos conforme especificado [aqui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa de:
- Um [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de instruções requer que você use CLI do Azure versão 2,0 ou posterior. Para confirmar a versão, no prompt de comando CLI do Azure, digite `az --version`. Para instalar ou atualizar, consulte [instalar CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Habilitar o crescimento automático do armazenamento do servidor MySQL

Habilite o armazenamento de crescimento automático do servidor em um servidor existente com o seguinte comando:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Habilite o armazenamento de crescimento automático do servidor ao criar um novo servidor com o seguinte comando:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md).