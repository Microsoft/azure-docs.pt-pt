---
title: Exemplo CLI - Grupo Failover - Instância Gerida Azure SQL
description: Script de exemplo Azure CLI para criar uma Instância Gerida Azure SQL, adicioná-lo a um grupo de failover, e testar falha.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e9efb4e222e74f97a4f3d88639b02a86a3a6d660
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84115434"
---
# <a name="use-cli-to-add-an-azure-sql-managed-instance-to-a-failover-group"></a>Utilize o CLI para adicionar um Azure SQL Managed Instance a um grupo de failover

Este exemplo do Azure CLI cria duas instâncias geridas, adiciona-as a um grupo de failover, e depois testa a falha da instância gerida primária para a instância gerida secundária.

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo. Terá de remover o grupo de recursos duas vezes. A remoção do grupo de recursos pela primeira vez removerá a instância gerida e os clusters virtuais, mas falhará com a mensagem de erro `az group delete : Long running operation failed with status 'Conflict'.` . Executar o grupo Az eliminar comando uma segunda vez para remover quaisquer recursos residuais, bem como o grupo de recursos.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| | |
|---|---|
| [az rede vnet](/cli/azure/network/vnet) | Comandos de rede virtuais.  |
| [az rede vnet subnet](/cli/azure/network/vnet/subnet) | Comandos de sub-rede de rede virtual. |
| [rede az nsg](/cli/azure/network/nsg) | Comandos do grupo de segurança da rede. |
| [az rede route-table](/cli/azure/network/route-table) | Comandos de mesa de rota. |
| [az sql mi](/cli/azure/sql/mi) | Comandos SQL Managed Instance. |
| [rede az público-ip](/cli/azure/network/public-ip) | Comandos de endereçoIP públicos da rede. |
| [az rede vnet-gateway](/cli/azure/network/vnet-gateway) | Comandos de Gateway de Rede Virtual. |
| [az sql instância-failover-grupo](/cli/azure/sql/instance-failover-group) | Comandos de grupo de falha de instância geridos sQL. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
