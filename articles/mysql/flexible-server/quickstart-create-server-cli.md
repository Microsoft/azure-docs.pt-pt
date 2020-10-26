---
title: 'Quickstart: Criar um servidor - Azure CLI - Azure Database for MySQL - Servidor Flexível'
description: Este quickstart descreve como usar o CLI Azure para criar uma Base de Dados Azure para o MySQL Flexible Server num grupo de recursos Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc
ms.openlocfilehash: 4be295ada476e4dc41a86d06908ef1d653a3bad8
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545026"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Quickstart: Criar uma base de dados Azure para o MySQL Flexible Server utilizando O Azure CLI

Este quickstart mostra como usar os comandos [Azure CLI](/cli/azure/get-started-with-azure-cli) em [Azure Cloud Shell](https://shell.azure.com) para criar uma Base de Dados Azure para o MySQL Flexible Server em cinco minutos. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O [Azure Cloud Shell](../../cloud-shell/overview.md) é uma concha interativa gratuita que pode usar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode abrir o Cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copy** para copiar os blocos de código, cole-o na Cloud Shell e selecione **Enter** para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este arranque rápido requer a versão 2.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](/cli/azure/reference-index#az-login) Note a propriedade **id,** que se refere ao **ID de subscrição** para a sua conta Azure.

```azurecli-interactive
az login
```

Selecione a subscrição específica sob a sua conta usando o comando [conjunto de conta az.](/cli/azure/account#az-account-set) Tome nota do valor de **id** da saída de **login az** para usar como valor para o argumento **de subscrição** no comando. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Para obter toda a sua subscrição, utilize [a lista de conta az](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Criar um servidor flexível

Crie um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md) utilizando o `az group create` comando e, em seguida, crie o seu servidor flexível MySQL dentro deste grupo de recursos. Deverá indicar um nome exclusivo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Crie um servidor flexível com o `az mysql flexible-server create` comando. Cada servidor pode conter várias bases de dados. O seguinte comando cria um servidor utilizando predefinições e valores de serviço a partir do [contexto local](/cli/azure/local-context)do seu Azure CLI: 

```azurecli
az mysql flexible-server create
```

O servidor criado tem os atributos abaixo: 
- Nome do servidor gerado automaticamente, nome de utilizador de administração, senha de administração, nome do grupo de recursos (se não especificado já no contexto local) e no mesmo local que o seu grupo de recursos 
- Predefinições de serviço para as restantes configurações do servidor: nível de cálculo (Burstable), tamanho de computação/SKU (B1MS), período de retenção de backup (7 dias) e versão MySQL (5.7)
- O método de conectividade padrão é o acesso privado (VNet Integration) com uma rede virtual gerada automaticamente e sub-rede

> [!NOTE] 
> O método de conectividade não pode ser alterado após a criação do servidor. Por exemplo, se selecionou *acesso privado (Integração VNet)* durante a criação, então não pode alterar para *acesso público (endereços IP permitidos)* após a criação. Recomendamos vivamente a criação de um servidor com acesso privado para aceder de forma segura ao seu servidor utilizando a Integração VNet. Saiba mais sobre o acesso privado no [artigo de conceitos.](./concepts-networking.md)

Se quiser alterar quaisquer incumprimentos, consulte a [documentação de referência](/cli/azure/mysql/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis. 

Abaixo está alguma saída de amostra: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Se quiser alterar quaisquer incumprimentos, consulte a [documentação de referência](/cli/azure/mysql/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis. 

> [!NOTE]
> As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Se for este o caso, não pode ligar ao servidor, a menos que o departamento de TI abra a porta 3306.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte o **fullyQualifiedDomainName** e o **administratorLogin** . Abaixo está uma amostra da saída JSON: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-using-mysql-command-line-client"></a>Conecte-se usando o cliente da linha de comando mysql

Uma vez que o servidor flexível foi criado com *acesso privado (VNet Integration)* , terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la à rede virtual criada. 

Uma vez criado o seu VM, pode entrar na máquina e instalar a ferramenta cliente popular, **[mysql.exe](https://dev.mysql.com/downloads/)** ferramenta de linha de comando.

Com mysql.exe, conecte-se utilizando o comando abaixo. Substitua os valores pelo nome e senha do seu servidor. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisa destes recursos para outro início rápido/tutorial, pode eliminá-los ao executar o seguinte comando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Se quiser eliminar o servidor recém-criado, pode executar o `az mysql server delete` comando.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Construa uma aplicação web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)