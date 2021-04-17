---
title: Configurar registos de auditoria com Azure CLI - Azure Database for MySQL - Servidor Flexível
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para o MySQL Flexible Server a partir do Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 757d765f44f09eeb6f7a24644abeb1ce4577f664
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509096"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Configurar e aceder a registos de auditoria para Azure Database for MySQL - Servidor Flexível utilizando o Azure CLI

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

O artigo mostra-lhe como configurar [registos de auditoria](concepts-audit-logs.md) para o seu servidor flexível MySQL utilizando o Azure CLI.

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

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!IMPORTANT]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Ativar e configurar a sessão de registo de auditoria.

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [registos de auditoria](concepts-audit-logs.md)
