---
title: Exemplo CLI- Enable BYOK TDE - Azure SQL Managed Instance
description: Aprenda a configurar uma Instância Gerida Azure SQL para começar a usar a Encriptação de Dados Transparente BYOK (TDE) para encriptação em repouso utilizando o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto
ms.date: 11/05/2019
ms.openlocfilehash: 2b948161633569d629dfb048a7d7dee6a9946f43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323693"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gerir encriptação de dados transparentes numa instância gerida usando a sua própria chave a partir do Cofre de Chaves Azure

Este exemplo de script do Azure CLI configura a Encriptação de Dados Transparente (TDE) com a chave gerida pelo cliente para Azure SQL Managed Instance, utilizando uma chave do Cofre de Chaves Azure. Isto é frequentemente referido como um cenário Bring Your Own Key para TDE. Para saber mais sobre o TDE com a chave gerida pelo cliente, consulte [O TDE Bring Your Own Key to Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md).

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="prerequisites"></a>Pré-requisitos

Uma instância gerida existente, consulte [Use Azure CLI para criar um Azure SQL Managed Instance](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Descrição |
|---|---|
| [az sql db](/cli/azure/sql/db) | Comandos de base de dados. |
| [az sql failover grupo](/cli/azure/sql/failover-group) | Comandos de grupo de failover. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
