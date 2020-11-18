---
title: 'Quickstart: Criar um servidor - Azure CLI - Azure Database for PostgreSQL - Servidor Flexível'
description: Este quickstart descreve como usar o CLI Azure para criar uma Base de Dados Azure para o Servidor Flexível PostgreSQL num grupo de recursos Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d43fab43b05ccfd88308390f58ba48ff9111b295
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842826"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Quickstart: Criar uma base de dados Azure para o Servidor Flexível PostgreSQL utilizando O Azure CLI

Este quickstart mostra como usar os comandos [Azure CLI](/cli/azure/get-started-with-azure-cli) em [Azure Cloud Shell](https://shell.azure.com) para criar uma Base de Dados Azure para o Servidor Flexível PostgreSQL em cinco minutos. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT] 
> A base de dados Azure para o PostgreSQL Flexible Server está atualmente em pré-visualização.

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

Crie um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md) utilizando o `az group create` comando e, em seguida, crie o seu servidor flexível PostgreSQL dentro deste grupo de recursos. Deverá indicar um nome exclusivo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie um servidor flexível com o `az postgres flexible-server create` comando. Cada servidor pode conter várias bases de dados. O seguinte comando cria um servidor utilizando predefinições e valores de serviço a partir do [contexto local](/cli/azure/local-context)do seu Azure CLI: 

```azurecli
az postgres flexible-server create
```

O servidor criado tem os atributos abaixo: 
- Nome do servidor gerado automaticamente, nome de utilizador de administração, senha de administração, nome do grupo de recursos (se não especificado já no contexto local) e no mesmo local que o seu grupo de recursos 
- Predefinições de serviço para as restantes configurações do servidor: nível de cálculo (Final geral), tamanho do cálculo/SKU (D2s_v3 - 2 vCore, 8 GB RAM), período de retenção de backup (7 dias) e versão PostgreSQL (12)
- O método de conectividade padrão é o acesso privado (VNet Integration) com uma rede virtual gerada automaticamente e sub-rede

> [!NOTE] 
> O método de conectividade não pode ser alterado após a criação do servidor. Por exemplo, se selecionou *acesso privado (Integração VNet)* durante a criação, então não pode alterar para *acesso público (endereços IP permitidos)* após a criação. Recomendamos vivamente a criação de um servidor com acesso privado para aceder de forma segura ao seu servidor utilizando a Integração VNet. Saiba mais sobre o acesso privado no [artigo de conceitos.](./concepts-networking.md)

Se quiser alterar quaisquer incumprimentos, consulte a documentação de referência do Azure CLI <!--FIXME --> para a lista completa de parâmetros CLI configuráveis. 

> [!NOTE]
> As ligações à Base de Dados Azure para PostgreSQL comunicam-se sobre a porta 5432. Se tentar ligar-se a partir de uma rede corporativa, o tráfego de saída sobre o porto 5432 pode não ser permitido. Se for esse o caso, não pode ligar-se ao seu servidor a menos que o seu departamento de TI abra a porta 5432.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte o **fullyQualifiedDomainName** e o **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>Conecte-se utilizando o cliente da linha de comando PostgreSQL

Uma vez que o servidor flexível foi criado com *acesso privado (VNet Integration)*, terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la à rede virtual criada. 

Uma vez criado o seu VM, pode entrar na máquina e instalar a ferramenta **[psql](https://www.postgresql.org/download/)** command-line.

Com o psql, conecte-se utilizando o comando abaixo. Substitua os valores pelo nome e senha do seu servidor. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisa destes recursos para outro início rápido/tutorial, pode eliminá-los ao executar o seguinte comando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Se quiser eliminar o servidor recém-criado, pode executar o `az postgres flexible-server delete` comando.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Implementar uma aplicação Django com App Service e PostgreSQL](tutorial-django-app-service-postgres.md)
