---
title: Registos de auditoria de acesso - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para MariaDB do Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384197"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configure e aceda aos registos de auditoria no Azure CLI

Pode configurar a Base de [Dados Azure para registos](concepts-audit-logs.md) de auditoria Da MariaDB a partir do Azure CLI.

> [!IMPORTANT]
> A funcionalidade de registo de auditoria encontra-se atualmente em pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia de como guiar, você precisa:

- [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no aviso de `az --version`comando Azure CLI, introduza . Para instalar ou atualizar, consulte [Instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configure a exploração madeireira de auditoria

Ativar e configurar o registo de auditoria utilizando os seguintes passos: 

1. Ligue os registos de auditoria, definindo o parâmetro **audit_logs_enabled** para "ON". 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os tipos de [eventos](concepts-audit-logs.md#configure-audit-logging) a iniciar por atualizar o parâmetro **audit_log_egitvents.**
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione qualquer utilizador MariaDB a ser excluído do registo através da atualização do parâmetro **audit_log_exclude_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione quaisquer utilizadores mariadb específicos para serem incluídos para o registo através da atualização do parâmetro **audit_log_include_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [registos](concepts-audit-logs.md) de auditoria na Base de Dados Azure para MariaDB
- Saiba como configurar registos de auditoria no [portal Azure](howto-configure-audit-logs-portal.md)