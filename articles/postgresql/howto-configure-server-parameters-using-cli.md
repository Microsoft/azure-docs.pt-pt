---
title: Configurar parâmetros-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como configurar parâmetros postgres no banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4e029428a3709bacdbcd50a6ac3714e730377242
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763628"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Personalizar parâmetros de configuração do servidor para o banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure
Você pode listar, mostrar e atualizar parâmetros de configuração para um servidor PostgreSQL do Azure usando a interface de linha de comando (CLI do Azure). Um subconjunto de configurações de mecanismo é exposto em nível de servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- Crie um banco de dados do Azure para servidor PostgreSQL e o banco de dados seguindo [criar um banco de dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instale [CLI do Azure](/cli/azure/install-azure-cli) interface de linha de comando em seu computador ou use o [Azure cloud Shell](../cloud-shell/overview.md) no portal do Azure usando o navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Listar parâmetros de configuração de servidor para o banco de dados do Azure para servidor PostgreSQL
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [AZ postgres Server Configuration List](/cli/azure/postgres/server/configuration) .

Você pode listar os parâmetros de configuração do servidor **mydemoserver.Postgres.Database.Azure.com** em grupo de recursos **MyResource**Group.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o comando [AZ postgres Server Configuration show](/cli/azure/postgres/server/configuration) .

Este exemplo mostra detalhes do parâmetro de configuração de servidor **log\_min\_messages** para o servidor **mydemoserver.Postgres.Database.Azure.com** em grupo de recursos **MyResource Group.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificar valor do parâmetro de configuração do servidor
Você também pode modificar o valor de um determinado parâmetro de configuração de servidor, que atualiza o valor de configuração subjacente para o mecanismo do servidor PostgreSQL. Para atualizar a configuração, use o comando [AZ postgres Server Configuration Set](/cli/azure/postgres/server/configuration) . 

Para atualizar o **log\_min\_mensagens** parâmetro de configuração de servidor do servidor **mydemoserver.Postgres.Database.Azure.com** em grupo de recursos **MyResource Group.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Se você quiser redefinir o valor de um parâmetro de configuração, basta optar por deixar o parâmetro `--value` opcional e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Esse comando redefine o **log\_min\_** a configuração de mensagens para o valor padrão **Warning**. Para obter mais informações sobre a configuração do servidor e os valores permitidos, consulte a documentação do PostgreSQL na [configuração do servidor](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Passos seguintes
- [Saiba como reiniciar um servidor](howto-restart-server-cli.md)
- Para configurar e acessar logs de servidor, consulte [logs de servidor no banco de dados do Azure para PostgreSQL](concepts-server-logs.md)
