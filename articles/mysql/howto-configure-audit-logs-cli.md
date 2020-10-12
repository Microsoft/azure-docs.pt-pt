---
title: Aceder a registos de auditoria - Azure CLI - Azure Database for MySQL
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para o MySQL a partir do Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 568f49565c6e6d8062f8869566cf3879b7c97eaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503332"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurar e aceder a registos de auditoria no CLI do Azure

Pode configurar a [Base de Dados Azure para os registos](concepts-audit-logs.md) de auditoria do MySQL a partir do Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia, precisa:

- [Base de Dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no pedido de comando do Azure CLI, insira `az --version` . Para instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!IMPORTANT]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Ativar e configurar o registo de auditoria utilizando os seguintes passos:

1. Ligue os registos de auditoria definindo o parâmetro **audit_logs_enabled** para "ON". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a registar atualizando o parâmetro **audit_log_events.**
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione quaisquer utilizadores do MySQL a serem excluídos do registo, atualizando o parâmetro **audit_log_exclude_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione quaisquer utilizadores específicos do MySQL a serem incluídos para iniciar sessão, atualizando o parâmetro **audit_log_include_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [os registos de auditoria](concepts-audit-logs.md) na Base de Dados Azure para o MySQL
- Saiba como configurar registos de auditoria no [portal Azure](howto-configure-audit-logs-portal.md)
