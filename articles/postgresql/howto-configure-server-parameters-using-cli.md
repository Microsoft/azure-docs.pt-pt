---
title: Configure parâmetros - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar parâmetros Postgres na Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4e029428a3709bacdbcd50a6ac3714e730377242
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74763628"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Personalize os parâmetros de configuração do servidor para a Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI
Pode listar, mostrar e atualizar parâmetros de configuração para um servidor Azure PostgreSQL utilizando a Interface da Linha de Comando (Azure CLI). Um subconjunto de configurações do motor é exposto ao nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- Crie uma Base de Dados Azure para servidor e base de dados PostgreSQL seguindo criar uma base de [dados Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instale a interface de linha de comando [Azure CLI](/cli/azure/install-azure-cli) na sua máquina ou utilize a [Casca de Nuvem Azure](../cloud-shell/overview.md) no portal Azure utilizando o seu navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista de parâmetros de configuração do servidor para base de dados Azure para servidor PostgreSQL
Para enumerar todos os parâmetros modificáveis num servidor e seus valores, executar o comando de configuração da lista de [servidores az postgres.](/cli/azure/postgres/server/configuration)

Pode listar os parâmetros de configuração do servidor para o servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, executar o comando de configuração do [servidor az postgres.](/cli/azure/postgres/server/configuration)

Este exemplo mostra detalhes do parâmetro de configuração do servidor de **mensagens min de log\_min\_** para **mydemoserver.postgres.database.azure.com** do servidor sob o grupo de **recursos myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor
Também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente ao motor do servidor PostgreSQL. Para atualizar a configuração, utilize o comando de configuração do [servidor az postgres.](/cli/azure/postgres/server/configuration) 

Para atualizar o parâmetro de configuração do servidor de **mensagens de log\_min\_** do servidor **mydemoserver.postgres.database.azure.com** do grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Se pretender redefinir o valor de um parâmetro de configuração, `--value` simplesmente opte por deixar de fora o parâmetro opcional e o serviço aplica o valor predefinido. Em exemplo acima, seria como:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Este comando reinicia a configuração das **mensagens de log\_min\_** para o valor padrão **AVISO**. Para obter mais informações sobre a configuração do servidor e valores admissíveis, consulte a documentação PostgreSQL na [Configuração do Servidor](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Passos seguintes
- [Saiba como reiniciar um servidor](howto-restart-server-cli.md)
- Para configurar e aceder aos registos do servidor, consulte registos de servidores na Base de [Dados Azure para PostgreSQL](concepts-server-logs.md)
