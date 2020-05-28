---
title: 'Azure CLI: Criar um caso gerido'
description: Roteiro de exemplo Azure CLI para criar uma instância gerida em 1º PqL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: fd2c7a33c0747aa21a8bda53ad5437c9e79a6557
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84115416"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Utilize o CLI para criar uma instância gerida azure SQL

Este exemplo de script Azure CLI cria um Azure SQL Managed Instance numa subnet dedicada dentro de uma nova rede virtual. Também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Uma vez que o script tenha sido executado com sucesso, a instância gerida pode ser acedida a partir da rede virtual ou de um ambiente no local. Consulte [Configure Azure VM para ligar a uma instância gerida azure SQL].. /.. /azure-sql/managed-instance/connect-vm-instance-configure.md) e [Configure uma ligação ponto-a-local a um Caso Gerido Azure SQL a partir do local](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](../../azure-sql/managed-instance/resource-limits.md#supported-regions) e [tipos de subscrição suportados.](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types)

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
| [az sql mi](/cli/azure/sql/mi) | Comandos SQL Managed Instance. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
