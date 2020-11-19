---
title: 'Quickstart: Criar uma piscina Sinapse SQL com Azure CLI'
description: Crie rapidamente uma piscina Sinapse SQL com uma regra de firewall ao nível do servidor utilizando o CLI Azure.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: Kevin
ms.custom: azure-synapse
ms.openlocfilehash: 59195bba69bb343e55cfcb7342400d93dcce60c0
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920755"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Quickstart: Criar uma piscina Sinapse SQL com Azure CLI

Crie um pool Sinapse SQL (armazém de dados) em Azure Synapse Analytics utilizando o Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Introdução

Utilize estes comandos para iniciar súm na Azure e criar um grupo de recursos.

1. Se estiver a utilizar uma instalação local, executar o comando [de login az](/cli/azure/reference-index#az_login) para iniciar sessão no Azure:

   ```azurecli
   az login
   ```

1. Se necessário, utilize o comando [conjunto de conta az](/cli/azure/account#az_account_set) para selecionar a sua subscrição:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Executar o [grupo az criar](/cli/azure/group#az_group_create) comando para criar um grupo de recursos:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Crie um [servidor SQL lógico](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) utilizando o [servidor az sql criar](/cli/azure/sql/server#az_sql_server_create) comando:

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   Os servidores contêm um grupo de bases de dados geridas como um grupo.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Criar uma [regra de firewall ao nível do servidor](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Uma regra de firewall ao nível do servidor permite que uma aplicação externa, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte a uma piscina SQL através da firewall do serviço de piscina SQL.

Executar o comando [de firewall do servidor az sql](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) para criar uma regra de firewall:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

Neste exemplo, a firewall só é aberta para outros recursos Azure. Para ativar a conectividade externa, altere o endereço IP para um endereço adequado para o seu ambiente. Para abrir todos os endereços IP, utilize 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

> [!NOTE]
> Os pontos finais SQL comunicam-se sobre a porta 1433. Se estiver a tentar ligar-se a partir de uma rede corporativa, o tráfego de saída sobre a porta 1433 pode não ser permitido pela firewall da sua rede. Em caso afirmativo, não poderá ligar-se ao seu servidor a menos que o seu departamento de TI abra a porta 1433.
>

## <a name="create-and-manage-your-sql-pool"></a>Crie e gere a sua piscina SQL

Crie a piscina SQL. Este exemplo utiliza o DW100c como o objetivo de serviço, que é um ponto de partida mais baixo para a sua piscina SQL.

> [!NOTE]
> Precisa de um espaço de trabalho previamente criado. Para obter mais informações, consulte [Quickstart: Crie um espaço de trabalho sinapse Azure com Azure CLI](../quickstart-create-workspace-cli.md).

Utilize a [piscina az sinapse sql criar](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) comando para criar a piscina SQL:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Para obter mais informações sobre as opções de [parâmetros, consulte a piscina de sql az sinaapse.](/cli/azure/ext/synapse/synapse/sql/pool)

Você pode ver suas piscinas SQL usando o comando [da lista de piscinas az sinapse sql:](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list)

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Utilize o comando [de az sinapse sql pool](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) para atualizar uma piscina existente:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Use o comando de pausa da [piscina az sinapse sql](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) para interromper a sua piscina:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Utilize o comando de currículo da [piscina az sinapse sql](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) para iniciar uma piscina pausada:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Para remover uma piscina SQL existente, utilize o comando de eliminação da [piscina az sinapse sql:](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete)

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido.

> [!TIP]
> Se planeia continuar a trabalhar com tutoriais de arranque rápido mais tarde, não limpe os recursos criados neste arranque rápido. Se não pretender continuar, utilize o comando de exclusão do [grupo AZ](/cli/azure/group#az_group_delete) para eliminar todos os recursos criados por este arranque rápido.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Criou agora uma piscina SQL, criou uma regra de firewall e está ligado à sua piscina SQL. Para saber mais, continue os dados de Carga no artigo [da piscina SQL.](load-data-from-azure-blob-storage-using-polybase.md)
