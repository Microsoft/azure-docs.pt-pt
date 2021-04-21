---
title: 'Quickstart: Criar um servidor - Azure CLI - Azure Database for MySQL'
description: Este guia de introdução descreve como utilizar a CLI do Azure para criar uma Base de Dados do Azure para o servidor MySQL num grupo de recursos do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3dd971ff36bde02072d14c465dbafec2823b256d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780322"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Quickstart: Criar uma base de dados Azure para servidor MySQL utilizando O Azure CLI

> [!TIP]
> Considere usar o comando [Az IL mais simples do Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) CLI (atualmente em pré-visualização). Experimente o [arranque rápido.](./quickstart-create-server-up-azure-cli.md)

Este quickstart mostra como usar os comandos [Azure CLI](/cli/azure/get-started-with-azure-cli) em [Azure Cloud Shell](https://shell.azure.com) para criar uma Base de Dados Azure para o servidor MySQL em cinco minutos. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este arranque rápido requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

 - Selecione a subscrição específica sob a sua conta usando o comando [conjunto de conta az.](/cli/azure/account) Tome nota do valor de **id** da saída de **login az** para usar como valor para o argumento **de subscrição** no comando. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Para obter toda a sua subscrição, utilize [a lista de conta az](/cli/azure/account#az_account_list).

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
Crie um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o [comando do grupo az](/cli/azure/group) e, em seguida, crie o seu servidor MySQL dentro deste grupo de recursos. Deverá indicar um nome exclusivo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie uma Base de Dados do Azure para o servidor MySQL com o comando [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create). Cada servidor pode conter várias bases de dados.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Aqui estão os detalhes dos argumentos acima: 

**Definição** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Introduza um nome único para a sua Base de Dados Azure para o servidor MySQL. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
resource-group | myResourceGroup | Indique o nome do grupo de recursos do Azure.
localização | westus | A localização do Azure para o servidor.
admin-user | myadmin | O nome de utilizador para o início de sessão do administrador. Não pode ser **azure_superuser**, **admin**, **administrador**, **raiz**, **convidado** nem **público**.
admin-password | *palavra-passe segura* | A palavra-passe do utilizador administrador. Tem de conter entre 8 e 128 carateres. A palavra-passe tem de conter carateres das três categorias seguintes: letras em maiúsculas do inglês, letras em minúsculas do inglês, números e carateres não alfanuméricos.
sku-name|GP_Gen5_2|Insira o nome do nível de preços e configuração de cálculo. Segue a convenção {escalão de preço}_{geração de computação}_{vCores} em estenografia. Consulte os [níveis de preços](./concepts-pricing-tiers.md) para obter mais informações.

>[!IMPORTANT] 
>- A versão padrão do MySQL no seu servidor é 5.7 . Atualmente temos versões 5.6 e 8.0 também disponíveis.
>- Para ver todos os argumentos para **o servidor az mysql criar** comando, consulte este documento de [referência](/cli/azure/mysql/server#az_mysql_server_create).
>- O SSL é ativado por padrão no seu servidor . Para obter mais infroamação em SSL, consulte [a conectividade Configure SSL](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor 
Por predefinição, o novo servidor criado está protegido com regras de firewall e não é acessível publicamente. Pode configurar a regra de firewall no seu servidor utilizando o comando [de criação de firewall do servidor az mysql.](/cli/azure/mysql/server/firewall-rule) Isto permitir-lhe-á ligar-se ao servidor localmente.

O exemplo seguinte cria uma regra de firewall chamada `AllowMyIP` que permite ligações a partir de um endereço IP específico, 192.168.0.1. Substitua o endereço IP a partir do quais estará a ligar. Pode utilizar uma gama de endereços IP, se necessário. Não sei como procurar o seu IP, então vá [https://whatismyipaddress.com/](https://whatismyipaddress.com/) para obter o seu endereço IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Se for este o caso, não pode ligar ao servidor, a menos que o departamento de TI abra a porta 3306.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Ligue-se à Base de Dados Azure para servidor MySQL utilizando o cliente da linha de comando mysql
Pode ligar-se ao seu servidor utilizando uma ferramenta de cliente popular, **[mysql.exe](https://dev.mysql.com/downloads/)** ferramenta de linha de comando com [a Azure Cloud Shell](../cloud-shell/overview.md). Em alternativa, pode utilizar a linha de comando Mysql no seu ambiente local.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Limpar os recursos
Se não precisa destes recursos para outro início rápido/tutorial, pode eliminá-los ao executar o seguinte comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se apenas quiser eliminar o único servidor recém-criado, pode executar o comando [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete).

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Construir uma aplicação PHP no Windows com o MySQL](../app-service/tutorial-php-mysql-app.md)