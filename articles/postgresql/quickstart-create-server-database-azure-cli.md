---
title: 'Quickstart: Criar servidor - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único'
description: Guia Quickstart para criar uma Base de Dados Azure para PostgreSQL - Servidor Único utilizando O Azure CLI (interface de linha de comando).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cb88f085e18526a17621d3c4960a5aad6db727ad
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496598"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Quickstart: Criar uma base de dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI

> [!TIP]
> Considere utilizar os [postgres az](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) mais simples até ao comando Azure CLI (atualmente em pré-visualização). Experimente o [arranque rápido.](./quickstart-create-server-up-azure-cli.md)

Este quickstart mostra como usar comandos [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) em [Azure Cloud Shell](https://shell.azure.com) para criar uma Base de Dados Azure para servidor PostgreSQL em cinco minutos.  Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) Note a propriedade **id** que se refere ao **ID de subscrição** para a sua conta Azure. 

```azurecli-interactive
az login
```

Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Tome nota do valor de **id** da saída de **login az** para usar como valor para o argumento **de subscrição** no comando. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Para obter toda a sua subscrição, utilize [a lista de conta az](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL

Crie um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o [comando de criação](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) do grupo az e, em seguida, crie o seu servidor PostgreSQL dentro deste grupo de recursos. Deverá indicar um nome exclusivo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie uma [Base de Dados do Azure para o servidor PostgreSQL](overview.md) com o comando [az postgres server create](/cli/azure/postgres/server). Cada servidor pode conter várias bases de dados.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Aqui estão os detalhes para os argumentos acima: 

**Definição** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Escolha um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor PostgreSQL. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
resource-group | myResourceGroup | Indique o nome do grupo de recursos do Azure.
localização | westus | A localização do Azure para o servidor.
admin-user | myadmin | O nome de utilizador para o início de sessão do administrador. Não pode ser **azure_superuser**, **admin**, **administrador**, **raiz**, **convidado** nem **público**.
admin-password | *palavra-passe segura* | A palavra-passe do utilizador administrador. Tem de conter entre 8 e 128 carateres. A palavra-passe tem de conter carateres das três categorias seguintes: letras em maiúsculas do inglês, letras em minúsculas do inglês, números e carateres não alfanuméricos.
sku-name|GP_Gen5_2|Insira o nome do nível de preços e configuração de cálculo. Segue a convenção {escalão de preço}_{geração de computação}_{vCores} em estenografia. Para mais informações, consulte [a Base de Dados Azure para postgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- A versão PostgreSQL predefinida no seu servidor é 9.6. Veja todas as nossas versões suportadas [aqui.](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)
>- Para ver todos os argumentos para **o servidor postgres az criar** comando, consulte este documento de [referência](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create)
>- O SSL é ativado por padrão no seu servidor. Para obter mais infroamação em SSL, consulte [a conectividade Configure SSL](./concepts-ssl-connection-security.md)

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor 
Por predefinição, o servidor criado não é acessível ao público e está protegido com regras de firewall. Pode configurar a regra de firewall no seu servidor utilizando o comando [de criação de firewall do servidor az postgres](/cli/azure/postgres/server/firewall-rule) para dar ao seu ambiente local acesso para se ligar ao servidor. 

O exemplo seguinte cria uma regra de firewall chamada `AllowMyIP` que permite ligações a partir de um endereço IP específico, 192.168.0.1. Substitua o endereço IP ou o intervalo de endereços IP que correspondam ao local de ligação.  Se não sabe como procurar o seu IP, vá [https://whatismyipaddress.com/](https://whatismyipaddress.com/) buscar o seu endereço IP.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  Certifique-se de que a firewall da sua rede permite a porta 5432 que é utilizada pela Azure Database para servidores PostgreSQL de forma a evitar problemas de conectividade. 

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte o **administratorLogin** e o **fullyQualifiedDomainName**.
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

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Ligue à Base de Dados de Azure para servidor PostgreSQL usando psql
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) é cliente popular usado para ligar aos servidores PostgreSQL. Pode ligar-se ao seu servidor utilizando **o psql** com [a Azure Cloud Shell](../cloud-shell/overview.md). Em alternativa, pode utilizar o psql no seu ambiente local se o tiver disponível. Uma base de dados vazia, 'postgres' já foi criada com o seu novo servidor PostgreSQL que pode usar para se conectar com o psql como mostrado abaixo 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se preferir utilizar um caminho URL para ligar aos Postgres, o URL codifica o sinal @ no nome de utilizador com `%40` . Por exemplo, a cadeia de ligação para psql seria,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>Limpar recursos
Se não precisar destes recursos para outro quickstart/tutorial, pode eliminá-los executando o seguinte comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se quiser eliminar o único servidor criado recentemente, pode executar o comando [az postgres server delete](/cli/azure/postgres/server).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
> 
> [Implementar uma aplicação web Django com PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Conecte-se com Node.JS app](./connect-nodejs.md)

