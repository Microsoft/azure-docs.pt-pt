---
title: Configurar parâmetros do servidor-CLI do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar os parâmetros de serviço no banco de dados do Azure para MySQL usando o utilitário de linha de comando CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 2a53debb72cfd5da73c2bceb7993288eb828237a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770531"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Personalizar parâmetros de servidor usando CLI do Azure
Você pode listar, mostrar e atualizar parâmetros de configuração para um servidor de banco de dados do Azure para MySQL usando CLI do Azure, o utilitário de linha de comando do Azure. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- [Um banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [CLI do Azure](/cli/azure/install-azure-cli) utilitário de linha de comando ou use o Azure cloud Shell no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Listar parâmetros de configuração do servidor para o servidor do banco de dados do Azure para MySQL
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [AZ MySQL Server Configuration List](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) .

Você pode listar os parâmetros de configuração do servidor **mydemoserver.mysql.Database.Azure.com** em grupo de recursos **MyResource**Group.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Para obter a definição de cada um dos parâmetros listados, consulte a seção de referência do MySQL em [variáveis de sistema do servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o comando [AZ MySQL Server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) .

Este exemplo mostra detalhes da **consulta de\_lenta\_** parâmetro de configuração do servidor de log para **mydemoserver.mysql.Database.Azure.com** do servidor no grupo de recursos **MyResource Group.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor de parâmetro de configuração de servidor
Você também pode modificar o valor de um determinado parâmetro de configuração de servidor, que atualiza o valor de configuração subjacente para o mecanismo do servidor MySQL. Para atualizar a configuração, use o comando [AZ MySQL Server Configuration Set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) . 

Para atualizar a **consulta de\_lenta\_** parâmetro de configuração do servidor de log do **mydemoserver.mysql.Database.Azure.com** do servidor no grupo de recursos **MyResource Group.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Se você quiser redefinir o valor de um parâmetro de configuração, omita o parâmetro opcional `--value` e o serviço aplicará o valor padrão. Para o exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Esse código redefine a **consulta de\_lenta\_** configuração de log para o valor padrão **desativado**. 

## <a name="working-with-the-time-zone-parameter"></a>Trabalhando com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Populando as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser populadas chamando o procedimento armazenado `az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou o MySQL Workbench.

> [!NOTE]
> Se você estiver executando o comando `az_load_timezone` do MySQL Workbench, talvez seja necessário desativar o modo de atualização segura primeiro usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Para exibir os valores de fuso horário disponíveis, execute o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurando o fuso horário de nível global

O fuso horário de nível global pode ser definido usando o comando [AZ MySQL Server Configuration Set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) .

O comando a seguir atualiza a **hora\_** parâmetro de configuração de servidor de zona do servidor **mydemoserver.mysql.Database.Azure.com** em grupo de recursos **MyResource** Group para **US/Pacífico**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Definindo o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser definido executando o comando `SET time_zone` de uma ferramenta como a linha de comando do MySQL ou o MySQL Workbench. O exemplo a seguir define o fuso horário para o fuso horário **dos EUA/Pacífico** .  

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MySQL para [funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Passos seguintes

- Como configurar [parâmetros de servidor no portal do Azure](howto-server-parameters.md)