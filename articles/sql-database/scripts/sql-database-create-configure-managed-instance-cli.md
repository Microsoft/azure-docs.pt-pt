---
title: 'Azure CLI: Criar um caso gerido'
description: Azure CLI exemplo script para criar um exemplo gerido em Azure SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497278"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Utilize o CLI para criar uma instância gerida Azure SQL

Este exemplo de script Azure CLI cria uma Instância Gerida Azure SQL numa sub-rede dedicada dentro de uma nova rede virtual. Também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Uma vez que o script tenha sido executado com sucesso, a instância gerida pode ser acedida a partir da rede virtual ou de um ambiente no local. Consulte [Configure Azure VM para ligar a um Azure SQL Managed Instance].. /.. /azure-sql/managed-instance/connect-vm-instance-configure.md) e [Configure uma ligação ponto-a-local a uma Instância Gerida Azure SQL a partir do local](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](../../azure-sql/managed-instance/resource-limits.md#supported-regions) e [tipos de assinaturas suportados.](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types)

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referência da amostra

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Descrição |
|---|---|
| [vnet de rede az](/cli/azure/network/vnet) | Comandos de rede virtual. |
| [sub-rede vnet de rede az](/cli/azure/network/vnet/subnet) | Comandos de sub-rede de rede virtual. |
| [az rede rota-mesa](/cli/azure/network/route-table) | Comandos de tabela de rota de rede. |
| [az sql mi](/cli/azure/sql/mi) | Comandos sql Gestd Instance. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da Base de Dados SQL adicionais na [Documentação da Base de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
