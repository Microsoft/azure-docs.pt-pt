---
title: Configure parâmetros do servidor - Azure CLI - Base de Dados Azure para MySQL
description: Este artigo descreve como configurar os parâmetros de serviço na Base de Dados Azure para MySQL utilizando o utilitário da linha de comando Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: ca5f80e57f90e4dd26ac2e4a175998ff3de2c102
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546424"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Personalize os parâmetros do servidor utilizando o Azure CLI
Pode listar, mostrar e atualizar parâmetros de configuração para uma Base de Dados Azure para servidor MySQL utilizando o Azure CLI, o utilitário da linha de comando Azure. Um subconjunto de configurações do motor é exposto ao nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- [Uma base de dados azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Utilitário de linha de comando [Azure CLI](/cli/azure/install-azure-cli) ou use a Casca de Nuvem Azure no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista de parâmetros de configuração do servidor para base de dados Azure para servidor MySQL
Para enumerar todos os parâmetros modificáveis num servidor e seus valores, executar o comando de configuração da lista de [servidores az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list)

Pode listar os parâmetros de configuração do servidor para o servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Para a definição de cada um dos parâmetros listados, consulte a secção de referência MySQL nas [Variáveis do Sistema de Servidores](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o comando de configuração do [servidor az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show)

Este exemplo mostra detalhes do parâmetro de configuração do servidor de **\_\_registo** de consulta lenta para **mydemoserver.mysql.database.azure.com** do servidor sob o grupo de **recursos myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor
Também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente ao motor do servidor MySQL. Para atualizar a configuração, utilize o comando de configuração do [servidor az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) 

Para atualizar o parâmetro de configuração do servidor de **\_\_registo** de consulta lenta do servidor **mydemoserver.mysql.database.azure.com** do grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Se pretender repor o valor de um parâmetro de `--value` configuração, omita o parâmetro opcional e o serviço aplica o valor predefinido. Para o exemplo acima, seria como:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Este código reinicia a configuração de registo de **\_\_consulta lenta** para o valor padrão **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro do fuso horário

### <a name="populating-the-time-zone-tables"></a>Povoar as tabelas do fuso horário

As tabelas de fuso horário no `mysql.az_load_timezone` seu servidor podem ser povoadas ligando para o procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada de trabalho MySQL.

> [!NOTE]
> Se estiver a `mysql.az_load_timezone` executar o comando a partir da Bancada de Trabalho MySQL, poderá ter de desligar o modo de atualização seguro primeiro utilizando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Deve reiniciar o servidor para garantir que as tabelas de fuso horário estão corretamente povoadas. Para reiniciar o servidor, utilize o [portal Azure](howto-restart-server-portal.md) ou [o CLI](howto-restart-server-cli.md).

Para ver os valores disponíveis do fuso horário, execute o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definição do fuso horário de nível global

O fuso horário de nível global pode ser definido utilizando o comando de configuração do [servidor az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set)

O comando seguinte atualiza o parâmetro de configuração do servidor do servidor de **fusos\_** **horários** do servidor mydemoserver.mysql.database.azure.com sob o grupo de **recursos myresourcegroup** para **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Definição do fuso horário do nível da sessão

O fuso horário do nível `SET time_zone` da sessão pode ser definido executando o comando a partir de uma ferramenta como a linha de comando MySQL ou a bancada de trabalho MySQL. O exemplo abaixo define o fuso horário para o fuso horário **EUA/Pacífico.**  

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação mySQL para funções de [data e hora.](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)


## <a name="next-steps"></a>Passos seguintes

- Como configurar parâmetros de [servidor no portal Azure](howto-server-parameters.md)