---
title: Registos de auditoria de acesso - Azure CLI - Base de Dados Azure para MySQL
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para MySQL do Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81384171"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configure e aceda aos registos de auditoria no Azure CLI

Pode configurar a Base de [Dados Azure para registos de auditoria MySQL](concepts-audit-logs.md) a partir do Azure CLI.

> [!IMPORTANT]
> A funcionalidade de registo de auditoria encontra-se atualmente em pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia de como guiar, você precisa:

- [Base de dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no aviso de `az --version`comando Azure CLI, introduza . Para instalar ou atualizar, consulte [Instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configure a exploração madeireira de auditoria

Ativar e configurar o registo de auditoria utilizando os seguintes passos:

1. Ligue os registos de auditoria, definindo o parâmetro **audit_logs_enabled** para "ON". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os tipos de [eventos](concepts-audit-logs.md#configure-audit-logging) a iniciar por atualização do parâmetro **audit_log_events.**
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione qualquer utilizador MySQL a ser excluído do registo através da atualização do **parâmetro audit_log_exclude_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione quaisquer utilizadores específicos do MySQL para serem incluídos para o registo através da atualização do parâmetro **audit_log_include_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [registos](concepts-audit-logs.md) de auditoria na Base de Dados Azure para mySQL
- Saiba como configurar registos de auditoria no [portal Azure](howto-configure-audit-logs-portal.md)