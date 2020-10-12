---
title: Parâmetros de configuração - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar os parâmetros postgres na Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4231f348f99073406fcb6a5bef9bf0f84cacf2eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707681"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Personalize os parâmetros de configuração do servidor para Azure Database para PostgreSQL - Servidor Único utilizando O Azure CLI
Pode listar, mostrar e atualizar parâmetros de configuração para um servidor Azure PostgreSQL utilizando a Interface da Linha de Comando (Azure CLI). Um subconjunto de configurações do motor é exposto ao nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- Criar uma base de dados Azure para servidor e base de dados PostgreSQL seguindo [criar uma base de dados de Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instale a interface de linha de comando [Azure CLI](/cli/azure/install-azure-cli) na sua máquina ou utilize o [Azure Cloud Shell](../cloud-shell/overview.md) no portal Azure utilizando o seu navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Listar os parâmetros de configuração do servidor para Azure Database para servidor PostgreSQL
Para listar todos os parâmetros modificáveis num servidor e os seus valores, execute o comando da lista de configuração do [servidor az postgres.](/cli/azure/postgres/server/configuration)

Pode listar os parâmetros de configuração do servidor para o **servidor mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração particular para um servidor, executar o comando [de configuração do servidor az postgres.](/cli/azure/postgres/server/configuration)

Este exemplo mostra detalhes do parâmetro de configuração do servidor de ** \_ \_ mensagens de registo min** para **servidor mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor
Também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente ao motor do servidor PostgreSQL. Para atualizar a configuração, utilize o comando [de configuração do servidor az postgres.](/cli/azure/postgres/server/configuration) 

Para atualizar o parâmetro de configuração do servidor de ** \_ \_ mensagens de registo min** de **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Se pretender redefinir o valor de um parâmetro de configuração, simplesmente optar por deixar de fora o `--value` parâmetro opcional, e o serviço aplica o valor predefinido. Acima de exemplo, pareceria:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Este comando reinicia a configuração de ** \_ \_ mensagens de log min** para o valor predefinido **AVISO**. Para obter mais informações sobre a configuração do servidor e valores admissíveis, consulte a documentação PostgreSQL na [Configuração do Servidor](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Passos seguintes
- [Saiba como reiniciar um servidor](howto-restart-server-cli.md)
- Para configurar e aceder aos registos de servidores, consulte [registos de servidores na Base de Dados de Azure para PostgreSQL](concepts-server-logs.md)
