---
title: Registos de auditoria de acesso - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para MariaDB a partir do Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/18/2020
ms.openlocfilehash: 670fd7f34b75ae05726053decdab7c15cdcdecc8
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100848"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurar e aceder a registos de auditoria no CLI do Azure

Pode configurar a [Base de Dados Azure para registos de auditoria MariaDB](concepts-audit-logs.md) do Azure CLI.

> [!IMPORTANT]
> A funcionalidade de registo de auditoria encontra-se atualmente em pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia, precisa:

- [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no pedido de comando do Azure CLI, insira `az --version` . Para instalar ou atualizar, consulte [instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!NOTE]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Ativar e configurar o registo de auditoria utilizando os seguintes passos: 

1. Ligue os registos de auditoria definindo o parâmetro **audit_logs_enabled** para "ON". 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a registar atualizando o parâmetro **audit_log_events.**
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione quaisquer utilizadores MariaDB a serem excluídos da exploração madeireira atualizando o parâmetro **audit_log_exclude_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione quaisquer utilizadores MariaDB específicos a serem incluídos para iniciar sessão, atualizando o parâmetro **audit_log_include_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os registos de auditoria](concepts-audit-logs.md) na Base de Dados Azure para MariaDB
- Saiba como configurar registos de auditoria no [portal Azure](howto-configure-audit-logs-portal.md)