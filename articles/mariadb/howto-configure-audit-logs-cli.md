---
title: Registos de auditoria de acesso - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para MariaDB a partir do Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1d446d8ee2863077ad84c361876758336f5a3cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665179"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Configurar e aceder à Base de Dados Azure para registos de auditoria da Maria DB no Azure CLI

Pode configurar a [Base de Dados Azure para registos de auditoria MariaDB](concepts-audit-logs.md) do Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia:

- Precisa de uma [base de dados Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!IMPORTANT]
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
