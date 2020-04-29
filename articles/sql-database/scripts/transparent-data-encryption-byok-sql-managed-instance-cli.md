---
title: Exemplo CLI- Habilitar BYOK TDE - Azure SQL Database Managed Instance
description: Aprenda a configurar um Caso Gerido Azure SQL para começar a usar encriptação transparente de dados (TDE) para encriptação em repouso usando powerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061724"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gerencie a encriptação de dados transparente sintetizado numa instância gerida utilizando a sua própria chave do Cofre de Chaves Azure

Este exemplo de script Azure CLI configura encriptação de dados transparentes (TDE) com chave gerida pelo cliente para a Instância Gerida Azure SQL, utilizando uma chave do Cofre de Chaves Azure. Isto é frequentemente referido como um cenário de trazer a sua própria chave para tDE. Para saber mais sobre o TDE com a chave gerida pelo cliente, consulte [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="prerequisites"></a>Pré-requisitos

Uma instância gerida existente, consulte Use Azure CLI para criar uma instância gerida pela Base de [Dados Azure SQL](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | Comandos de base de dados. |
| [az sql failover-grupo](/cli/azure/sql/failover-group) | Falha de comandos de grupo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../sql-database-cli-samples.md).
