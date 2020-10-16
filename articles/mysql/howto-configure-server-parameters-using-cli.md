---
title: Configurar parâmetros de servidor - Azure CLI - Azure Database for MySQL
description: Este artigo descreve como configurar os parâmetros de serviço na Base de Dados Azure para o MySQL utilizando o utilitário da linha de comando Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: dfb1d59335f2e35ce3d4661f043b6eed53d2a109
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627164"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Configurar os parâmetros do servidor na Base de Dados Azure para o MySQL utilizando o Azure CLI
Pode listar, mostrar e atualizar parâmetros de configuração para uma Base de Dados Azure para o servidor MySQL utilizando o Azure CLI, o utilitário da linha de comando Azure. Um subconjunto de configurações do motor é exposto ao nível do servidor e pode ser modificado. 

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente ao nível do servidor, utilizar o [portal Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md)ou [Azure](./howto-server-parameters.md)

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- [Uma base de dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Utilitário da linha de comando [Azure CLI](/cli/azure/install-azure-cli) ou use o Azure Cloud Shell no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Listar os parâmetros de configuração do servidor para Azure Database para o servidor MySQL
Para listar todos os parâmetros modificáveis num servidor e os seus valores, execute o comando da lista de configuração do [servidor az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list)

Pode listar os parâmetros de configuração do servidor para o **servidor mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Para a definição de cada um dos parâmetros listados, consulte a secção de referência MySQL nas [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração particular para um servidor, executar o comando de configuração do [servidor az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show)

Este exemplo mostra detalhes do parâmetro de configuração do servidor ** \_ de \_ registo** de consulta lenta para **mydemoserver.mysql.database.azure.com** de servidor sob o grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor
Também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente ao motor do servidor MySQL. Para atualizar a configuração, utilize o comando de configuração do [servidor az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) 

Para atualizar o parâmetro de configuração do servidor de ** \_ \_ registo** de consulta lenta do servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Se pretender redefinir o valor de um parâmetro de configuração, omita o parâmetro opcional `--value` e o serviço aplica o valor predefinido. Para o exemplo acima, pareceria:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Este código reinicia a configuração **de \_ registo de \_ consulta lenta** para o valor predefinido **OFF**. 

## <a name="setting-parameters-not-listed"></a>Definição de parâmetros não listados
Se o parâmetro do servidor que pretende atualizar não estiver listado no portal Azure, pode configurar opcionalmente o parâmetro ao nível de ligação utilizando `init_connect` . Isto define os parâmetros do servidor para cada cliente que se conecta ao servidor. 

Atualize o parâmetro de configuração do servidor de ** \_ ligação init** de **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup** para definir valores como o conjunto de caracteres.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro do fuso horário

### <a name="populating-the-time-zone-tables"></a>Povoando as tabelas do fuso horário

As tabelas de fuso horário do seu servidor podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench.

> [!NOTE]
> Se estiver a executar o `mysql.az_load_timezone` comando a partir da bancada MySQL Workbench, poderá ter de desligar o modo de atualização segura primeiro utilizando `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Deve reiniciar o servidor para garantir que as tabelas de fuso horário estão devidamente povoadas. Para reiniciar o servidor, utilize o [portal Azure](howto-restart-server-portal.md) ou [CLI](howto-restart-server-cli.md).

Para visualizar os valores do fuso horário disponível, executar o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definição do fuso horário de nível global

O fuso horário de nível global pode ser definido usando o comando de configuração do [servidor az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set)

O comando seguinte atualiza o parâmetro de configuração do servidor do fuso ** \_ horário** do servidor **mydemoserver.mysql.database.azure.com** no âmbito do grupo de recursos **myresourcegroup** para **EUA/Pacífico**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Definição do fuso horário do nível da sessão

O fuso horário de nível de sessão pode ser definido executando o `SET time_zone` comando a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. O exemplo abaixo define o fuso horário para o **fuso horário EUA/Pacífico.**  

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação mySQL para [funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Passos seguintes

- Como configurar [parâmetros do servidor no portal Azure](howto-server-parameters.md)
