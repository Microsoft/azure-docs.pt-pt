---
title: Configurar parâmetros do servidor-CLI do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar os parâmetros de serviço no banco de dados do Azure para MariaDB usando o utilitário de linha de comando CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 6616bd8172e9bc049a6e0e2c687390197de2f391
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767318"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalizar parâmetros de configuração do servidor usando CLI do Azure
Você pode listar, mostrar e atualizar parâmetros de configuração para um banco de dados do Azure para MariaDB Server usando CLI do Azure, o utilitário de linha de comando do Azure. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- [Um banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [CLI do Azure](/cli/azure/install-azure-cli) utilitário de linha de comando ou use o Azure cloud Shell no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Listar parâmetros de configuração do servidor para o banco de dados do Azure para MariaDB Server
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [AZ MariaDB Server Configuration List](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) .

Você pode listar os parâmetros de configuração do servidor **mydemoserver.MariaDB.Database.Azure.com** em grupo de recursos **MyResource**Group.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Para obter a definição de cada um dos parâmetros listados, consulte a seção de referência do MariaDB em [variáveis de sistema do servidor](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o comando [AZ MariaDB Server Configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) .

Este exemplo mostra detalhes da **consulta de\_lenta\_** parâmetro de configuração do servidor de log para **mydemoserver.MariaDB.Database.Azure.com** do servidor no grupo de recursos **MyResource Group.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor de parâmetro de configuração de servidor
Você também pode modificar o valor de um determinado parâmetro de configuração de servidor, que atualiza o valor de configuração subjacente para o mecanismo do servidor MariaDB. Para atualizar a configuração, use o comando [AZ MariaDB Server Configuration Set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) . 

Para atualizar a **consulta de\_lenta\_** parâmetro de configuração do servidor de log do **mydemoserver.MariaDB.Database.Azure.com** do servidor no grupo de recursos **MyResource Group.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Se você quiser redefinir o valor de um parâmetro de configuração, omita o parâmetro opcional `--value` e o serviço aplicará o valor padrão. Para o exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Esse código redefine a **consulta de\_lenta\_** configuração de log para o valor padrão **desativado**. 

## <a name="working-with-the-time-zone-parameter"></a>Trabalhando com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Populando as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser populadas chamando o procedimento armazenado `az_load_timezone` de uma ferramenta como a linha de comando MariaDB ou o MariaDB Workbench.

> [!NOTE]
> Se você estiver executando o comando `az_load_timezone` do MariaDB Workbench, talvez seja necessário desativar o modo de atualização segura primeiro usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Para exibir os valores de fuso horário disponíveis, execute o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurando o fuso horário de nível global

O fuso horário de nível global pode ser definido usando o comando [AZ MariaDB Server Configuration Set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) .

O comando a seguir atualiza a **hora\_** parâmetro de configuração de servidor de zona do servidor **mydemoserver.MariaDB.Database.Azure.com** em grupo de recursos **MyResource** Group para **US/Pacífico**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Definindo o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser definido executando o comando `SET time_zone` de uma ferramenta como a linha de comando MariaDB ou o MariaDB Workbench. O exemplo a seguir define o fuso horário para o fuso horário **dos EUA/Pacífico** .  

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MariaDB para [funções de data e hora](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Passos seguintes

- Como configurar [parâmetros de servidor no portal do Azure](howto-server-parameters.md)
