---
title: 'Quickstart: Criar servidor - Azure CLI - Azure Database for PostgreSQL - servidor único'
description: Neste guia de arranque rápido, irá criar uma Base de Dados Azure para o servidor PostgreSQL utilizando o Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 182226a0bb97c9239f1c0d0dc6e7d4d2b8bdbb1c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798878"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Quickstart: Criar uma Base de Dados Azure para servidor PostgreSQL utilizando o Azure CLI

Este quickstart mostra como usar comandos [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) em [Azure Cloud Shell](https://shell.azure.com) para criar uma única Base de Dados Azure para servidor PostgreSQL em cinco minutos. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!TIP]
> Considere usar os [postgres az](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) mais simples até o comando Azure CLI que está atualmente em pré-visualização. Experimente o [arranque rápido.](./quickstart-create-server-up-azure-cli.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo requer que você execute a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Tem de iniciar sessão na sua conta utilizando o comando [de login az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) Note a propriedade **id,** que se refere ao **ID de subscrição** para a sua conta Azure. 

```azurecli-interactive
az login
```

Selecione o ID de subscrição específico na sua conta utilizando o comando [conjunto de conta az.](/cli/azure/account) Tome nota do valor de **id** da saída de **login az** para usar como o valor para o argumento de **subscrição** no comando. 

```azurecli
az account set --subscription <subscription id>
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Para obter todas as suas subscrições, utilize [a lista de conta az](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL

Crie um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o [comando de criação](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) do grupo az e, em seguida, crie o seu servidor PostgreSQL dentro deste grupo de recursos. Deverá indicar um nome exclusivo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie uma [Base de Dados Azure para o servidor PostgreSQL](overview.md) utilizando o [servidor postgres az criar](/cli/azure/postgres/server) comando. Cada servidor pode conter várias bases de dados.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Aqui estão os detalhes dos argumentos anteriores: 

**Definição** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Nome único que identifica a sua Base de Dados Azure para servidor PostgreSQL. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Deve conter 3 a 63 caracteres.
resource-group | myResourceGroup | Nome do grupo de recursos Azure.
localização | westus | Localização azul para o servidor.
admin-user | myadmin | Nome de utilizador para o login do administrador. Não pode ser **azure_superuser,** **administrador,** **raiz,** **hóspede,** ou **público.** **root**
admin-password | *palavra-passe segura* | Palavra-passe do utilizador administrador. Deve conter 8 a 128 caracteres de três das seguintes categorias: letras maiúsculas inglesas, letras minúsculas, números e caracteres não alfanuméricos.
sku-name|GP_Gen5_2| Nome da configuração do nível de preços e do cálculo. Siga a convenção {nível de preços}_{compute generation}_{vCores} em abreviatura. Para obter mais informações, consulte [a Base de Dados Azure para obter preços pós-SQL.](https://azure.microsoft.com/pricing/details/postgresql/server/)

>[!IMPORTANT] 
>- A versão PostgreSQL predefinida no seu servidor é 9.6. Para ver todas as versões suportadas, consulte [as principais versões postgresQL suportadas.](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)
>- Para ver todos os argumentos para **o servidor az postgres criar** comando, consulte este documento de [referência](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).
>- O SSL é ativado por padrão no seu servidor. Para obter mais informações sobre o SSL, consulte [a conectividade Configure SSL](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor 
Por predefinição, o servidor que criou não é acessível ao público e está protegido com regras de firewall. Pode configurar as regras de firewall no seu servidor utilizando o comando [de criação de firewall do servidor az postgres](/cli/azure/postgres/server/firewall-rule) para dar ao seu ambiente local acesso para se ligar ao servidor. 

O exemplo seguinte cria uma regra de firewall chamada `AllowMyIP` que permite ligações a partir de um endereço IP específico, 192.168.0.1. Substitua o endereço IP ou o intervalo de endereços IP que corresponda ao local de ligação. Se não sabe o seu endereço IP, vá a [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) buscá-lo.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Para evitar problemas de conectividade, certifique-se de que a firewall da sua rede permite a porta 5432. Azure Database para servidores PostgreSQL utiliza essa porta. 

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para se ligar ao seu servidor, forneça informações do anfitrião e credenciais de acesso.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Tome nota dos valores do **AdministradorLogin** e **dos valores doDomainName totalmente Qualificado.**

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Ligue-se à Base de Dados Azure para o servidor PostgreSQL utilizando o psql
O cliente [psql](https://www.postgresql.org/docs/current/static/app-psql.html) é uma escolha popular para ligar aos servidores PostgreSQL. Pode ligar-se ao seu servidor utilizando o psql com [a Azure Cloud Shell](../cloud-shell/overview.md). Você também pode usar psql no seu ambiente local se você tiver disponível. Uma base de dados vazia, **postgres,** é criada automaticamente com um novo servidor PostgreSQL. Pode utilizar essa base de dados para se conectar com o psql, como mostra o código seguinte. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Se preferir utilizar um caminho URL para ligar aos Postgres, o URL codifica o sinal @ no nome de utilizador com `%40` . Por exemplo, a cadeia de ligação para psql seria:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Limpar os recursos
Se não precisar destes recursos para outro arranque rápido ou tutorial, pode eliminá-los executando o seguinte comando. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se apenas pretender eliminar o servidor recém-criado, pode executar o [comando de eliminação do servidor az postgres.](/cli/azure/postgres/server)

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a sua base de dados utilizando exportação e importação](./howto-migrate-using-export-and-import.md)
> 
> [Implementar uma aplicação web Django com PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Conecte-se com uma aplicação Node.JS](./connect-nodejs.md)

