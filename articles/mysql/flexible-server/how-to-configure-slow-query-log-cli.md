---
title: Configurar registos de auditoria e registos de consulta lenta com Azure CLI - Base de Dados Azure para MySQL - Servidor Flexível
description: Este artigo descreve como configurar e aceder a registos de consulta lenta na Base de Dados Azure para o MySQL Flexible Server a partir do Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b42aba84dcbff795ee4ca1f8d622527e8039f27a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509144"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Configurar registos de consulta lenta para Azure Database for MySQL - Servidor Flexível utilizando o Azure CLI

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

O artigo mostra-lhe como configurar [registos de consulta lenta](concepts-slow-query-logs.md) para o seu servidor flexível MySQL utilizando o Azure CLI. 

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instale ou atualize o Azure CLI para a versão mais recente. Consulte [a instalação do Azure CLI](/cli/azure/install-azure-cli).
-  Faça login na conta Azure utilizando o comando [de login az.](/cli/azure/reference-index#az-login) Note a propriedade **id,** que se refere ao **ID de subscrição** para a sua conta Azure.

    ```azurecli-interactive
    az login
    ````

- Se tiver várias subscrições, escolha a subscrição apropriada na qual pretende criar o servidor utilizando o ```az account set``` comando.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Crie um MySQL Flexible Server se ainda não tiver criado um utilizando o ```az mysql flexible-server create``` comando.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-slow-query-logs"></a>Configurar registos de consulta lenta

>[!IMPORTANT]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Ative e configure registos de consulta lenta para o seu servidor.

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [registos de consulta lenta](concepts-slow-query-logs.md)
