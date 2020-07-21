---
title: 'Azure CLI: Adicionar instância gerida ao grupo failover'
description: Azure CLI exemplo script para criar um Azure SQL Managed Instance, adicioná-lo a um grupo de failover, e testar failover.
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
ms.openlocfilehash: bf4e2b04c540ee5672bea6e097496ced5a0d622a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528881"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>Use o CLI para criar um Azure SQL Managed Instance para um grupo de failover

Este exemplo do Azure CLI cria duas instâncias geridas, adiciona-as a um grupo de failover, e depois testa o failover da instância gerida primária para o caso secundário gerido.

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Scripts de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados. Terá de remover o grupo de recursos duas vezes. Remover o grupo de recursos pela primeira vez removerá a instância gerida e os clusters virtuais, mas depois falhará com a mensagem de erro `az group delete : Long running operation failed with status 'Conflict'.` . Executar o grupo az eliminar o comando uma segunda vez para remover quaisquer recursos residuais, bem como o grupo de recursos.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Descrição |
|---|---|
| [vnet de rede az](/cli/azure/network/vnet) | Comandos de rede virtual.  |
| [sub-rede vnet de rede az](/cli/azure/network/vnet/subnet) | Comandos de sub-rede de rede virtual. |
| [rede az nsg](/cli/azure/network/nsg) | Comandos do grupo de segurança da rede. |
| [regra nsg rede az](/cli/azure/network/nsg/rule)| Comandos de regras de segurança de rede. |
| [az rede rota-mesa](/cli/azure/network/route-table) | Comandos da tabela de rotas. |
| [az sql mi](/cli/azure/sql/mi) | Comandos sql Gestd Instance. |
| [az rede público-ip](/cli/azure/network/public-ip) | Comandos de endereço IP públicos de rede. |
| [az rede vnet-gateway](/cli/azure/network/vnet-gateway) | Comandos Virtual Network Gateway |
| [az sql instância-failover-grupo](/cli/azure/sql/instance-failover-group) | SQL Managed Instance falha comandos de grupo. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
