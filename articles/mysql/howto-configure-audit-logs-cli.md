---
title: Aceder a registos de auditoria - Azure CLI - Azure Database for MySQL
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para o MySQL a partir do Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7ffabae785a589bacf349356079f6046039f9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542002"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurar e aceder a registos de auditoria no CLI do Azure

Pode configurar a [Base de Dados Azure para os registos](concepts-audit-logs.md) de auditoria do MySQL a partir do Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia:

- Precisa de uma [base de dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

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
