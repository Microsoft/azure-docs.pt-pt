---
title: Exemplo CLI - crie uma instância gerida na Base de Dados Azure SQL
description: Roteiro de exemplo Azure CLI para criar uma instância gerida na Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067451"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Utilize o CLI para criar uma instância gerida pela Base de Dados Azure SQL

Este exemplo de script Azure CLI cria uma base de dados Azure SQL gerida numa subnet dedicada dentro de uma nova rede virtual. Também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Uma vez que o script tenha sido executado com sucesso, a instância gerida pode ser acedida a partir da rede virtual ou de um ambiente no local. Consulte o [Configure Azure VM para ligar a uma instância gerida](../sql-database-managed-instance-configure-vm.md) pela Base de Dados Azure SQL e [configurar uma ligação ponto-a-local a uma base de dados Azure SQL Gerida a partir do local](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](../sql-database-managed-instance-resource-limits.md#supported-regions) e [tipos de subscrição suportados.](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| | |
|---|---|
| [az rede vnet](/cli/azure/network/vnet) | Comandos de rede virtuais. |
| [az rede vnet subnet](/cli/azure/network/vnet/subnet) | Comandos de sub-rede de rede virtual. |
| [az rede route-table](/cli/azure/network/route-table) | Comandos de mesa de rota de rede. |
| [az sql mi](/cli/azure/sql/mi) | Comandos de instância geridos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../sql-database-cli-samples.md).
