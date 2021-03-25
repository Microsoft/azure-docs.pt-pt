---
title: Configurar parâmetros de servidor - Azure CLI - Base de Dados Azure para O Servidor Flexível MySQL
description: Este artigo descreve como configurar os parâmetros de serviço na Base de Dados Azure para o servidor flexível MySQL utilizando o utilitário da linha de comando Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 21f9b3dcb94be105c8b7279e6ac447540da65447
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110105"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Configurar os parâmetros do servidor na Base de Dados Azure para o MySQL Flexible Server utilizando o Azure CLI

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Pode listar, mostrar e atualizar parâmetros para uma Base de Dados Azure para servidor flexível MySQL utilizando o Azure CLI, o utilitário da linha de comando Azure. Os parâmetros do servidor são configurados com o valor predefinido e recomendado quando cria o servidor.  

Este artigo descreve como listar, mostrar e atualizar os parâmetros do servidor utilizando o CLI Azure.

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente ao nível do servidor, utilizar o portal [Azure CLI](./how-to-configure-server-parameters-cli.md) ou [Azure](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- [Uma base de dados Azure para o servidor flexível MySQL](quickstart-create-server-cli.md)
- Utilitário da linha de comando [Azure CLI](/cli/azure/install-azure-cli) ou use o Azure Cloud Shell no navegador.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Listar os parâmetros do servidor para Azure Database para o servidor flexível MySQL
Para listar todos os parâmetros de um servidor e seus valores, executar o comando [da lista de parâmetros az mysql flexível-servidor.](/cli/azure/mysql/flexible-server/parameter)

Pode listar os parâmetros do servidor para o **servidor mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Para a definição de cada um dos parâmetros listados, consulte a secção de referência MySQL nas [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-parameter-details"></a>Mostrar detalhes do parâmetro do servidor
Para mostrar detalhes sobre um parâmetro específico para um servidor, executar o comando [de indicação de parâmetros flexíveis az mysql.](/cli/azure/mysql/flexible-server/parameter)

Este exemplo mostra detalhes do parâmetro do servidor de **\_ \_ registo** de consulta lenta para **mydemoserver.mysql.database.azure.com de** servidor sob o grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Modificar o valor do parâmetro do servidor
Também pode modificar o valor de um determinado parâmetro do servidor, que atualiza o valor de configuração subjacente ao motor do servidor MySQL. Para atualizar o parâmetro do servidor, utilize o comando [de definição de parâmetros flexíveis az mysql.](/cli/azure/mysql/flexible-server/parameter) 

Para atualizar o parâmetro do servidor de **\_ \_ registo** de consulta lenta do servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Se pretender redefinir o valor de um parâmetro, omita o parâmetro opcional `--value` e o serviço aplica o valor predefinido. Para o exemplo acima, pareceria:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Este código reinicia o **\_ \_ registo de consulta lenta** ao valor predefinido **OFF**. 

## <a name="setting-non-modifiable-server-parameters"></a>Definição de parâmetros de servidor não modificáveis

Se o parâmetro do servidor que pretende atualizar não for modificável, pode configurar opcionalmente o parâmetro ao nível de ligação utilizando `init_connect` . Isto define os parâmetros do servidor para cada cliente que se conecta ao servidor. 

Atualize o parâmetro do servidor **\_ de ligação init** de **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup** para definir valores como o conjunto de caracteres.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> Pode utilizar `init_connect` para alterar parâmetros que não exigem SUPER privilégio(s) ao nível da sessão. Para verificar se pode definir o parâmetro através de `init_connect`, execute o comando `set session parameter_name=YOUR_DESIRED_VALUE;` e se tiver erros como **Acesso negado; precisa de SUPER privilégio(s)** , não pode definir o parâmetro com “init_connect”.

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro do fuso horário

### <a name="populating-the-time-zone-tables"></a>Povoando as tabelas do fuso horário

As tabelas de fuso horário do seu servidor podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench.

> [!NOTE]
> Se estiver a executar o `mysql.az_load_timezone` comando a partir da bancada MySQL Workbench, poderá ter de desligar o modo de atualização segura primeiro utilizando `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Deve reiniciar o servidor para garantir que as tabelas de fuso horário estão devidamente povoadas.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Para visualizar os valores do fuso horário disponível, executar o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definição do fuso horário de nível global

O fuso horário de nível global pode ser definido usando o comando [de parâmetro flexível az mysql.](/cli/azure/mysql/flexible-server/parameter)

O comando seguinte atualiza o parâmetro do servidor de **\_ fuso horário** do servidor **mydemoserver.mysql.database.azure.com** no âmbito do grupo de recursos **myresourcegroup** para **EUA/Pacífico**.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Definição do fuso horário do nível da sessão

O fuso horário de nível de sessão pode ser definido executando o `SET time_zone` comando a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. O exemplo abaixo define o fuso horário para o **fuso horário EUA/Pacífico.**  

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação mySQL para [funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Passos seguintes

- Como configurar [parâmetros do servidor no portal Azure](./how-to-configure-server-parameters-portal.md)
