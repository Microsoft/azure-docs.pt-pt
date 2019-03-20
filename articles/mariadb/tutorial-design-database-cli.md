---
title: 'Tutorial: Conceber uma base de dados do Azure para MariaDB com a CLI do Azure'
description: Este tutorial explica como criar e gerir a base de dados do Azure para MariaDB servidor e base de dados com o CLI do Azure a partir da linha de comando.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/10/2018
ms.custom: mvc
ms.openlocfilehash: 548f4f10758b2d69bf4fda00f8bf52d33d20306c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999157"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Tutorial: Conceber uma base de dados do Azure para MariaDB com a CLI do Azure

Base de dados do Azure para MariaDB é um serviço de base de dados relacional na cloud da Microsoft baseado no motor de base de dados de MariaDB Community Edition. Neste tutorial, utiliza a CLI do Azure (interface de linha de comandos) e outros utilitários para saber como:

> [!div class="checklist"]
> * Criar um Azure Database for MariaDB
> * Configurar a firewall do servidor
> * Utilize a [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

Pode utilizar o Azure Cloud Shell no browser ou [instalar a CLI do Azure]( /cli/azure/install-azure-cli) no computador para executar os blocos de código deste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso existe ou é cobrado. Selecione um ID de subscrição específica na sua conta com o comando [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) com o comando [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um Azure Database for MariaDB Server
Criar uma base de dados do Azure para o servidor da MariaDB com a `az mariadb server create` comando. Cada servidor pode gerir múltiplas bases de dados. Geralmente, é utilizada uma base de dados em separado para cada projeto ou para cada utilizador.

O exemplo seguinte cria uma base de dados do Azure para servidor MariaDB localizado em `westus` no grupo de recursos `myresourcegroup` com o nome `mydemoserver`. O servidor tem um início de sessão de administrador com o nome `myadmin`. É para fins gerais, servidor de geração 5 com 2 vCores. Substitua `<server_admin_password>` pelo seu próprio valor.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
O valor do parâmetro sku-name segue a convenção {escalão de preço} \_ {geração de computação} \_ {vCores}, conforme os exemplos abaixo:
+ `--sku-name B_Gen5_4` mapeia para Básico, Ger 5 e 4 vCores.
+ `--sku-name GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
+ `--sku-name MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Leia a documentação dos [escalões de preços](./concepts-pricing-tiers.md) para entender os valores válidos por região e por escalão.

> [!IMPORTANT]
> O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste início rápido. Lembre-se ou grave estas informações para utilização posterior.


## <a name="configure-firewall-rule"></a>Configurar a regra de firewall
Criar uma base de dados do Azure para a regra de firewall ao nível do servidor da MariaDB com a `az mariadb server firewall-rule create` comando. Uma regra de firewall ao nível do servidor permite que uma aplicação externa, como **mysql** ferramenta da linha de comandos ou o MySQL Workbench ligar ao seu servidor através da firewall do serviço Azure MariaDB.

O exemplo seguinte cria uma regra de firewall chamada `AllowMyIP` que permite ligações a partir de um endereço IP específico, 192.168.0.1. Substitua o endereço IP ou intervalo de endereços IP que correspondem ao local onde os irá ligar.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Ligar ao servidor com o mysql
Utilize a [ferramenta de linha de comandos mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para estabelecer uma ligação ao servidor do Azure Database for MariaDB. Neste exemplo, o comando é:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Criar uma base de dados vazia
Assim que estiver ligado ao servidor, crie uma base de dados vazia.
```sql
mysql> CREATE DATABASE mysampledb;
```

Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados recentemente criada:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados
Agora que sabe como ligar à base de dados do Azure para MariaDB base de dados, conclua algumas tarefas básicas.

Em primeiro lugar, crie uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que armazena informações de inventário.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas
Agora que tem uma tabela, insira alguns dados na mesma. Na janela da linha de comandos aberta, execute a seguinte consulta para inserir algumas linhas de dados.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tem agora duas linhas de dados de exemplo nas tabelas que criou anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para obter informações da tabela de base de dados.
```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados nas tabelas.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha é atualizada em conformidade ao obter dados.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo
Imagine que eliminou acidentalmente esta tabela. É algo de que não é fácil recuperar. Base de dados do Azure para MariaDB permite-lhe voltar atrás para qualquer ponto anterior no tempo o até aos últimos 35 dias e restaurar este ponto anterior no tempo para um novo servidor. Pode utilizar este servidor novo para recuperar os dados eliminados. Os passos seguintes restauram o servidor de exemplo para um ponto antes da tabela ter sido adicionada.

Para o restauro, precisa das seguintes informações:

- Ponto de restauro: Selecione um ponto anterior no tempo que ocorre antes do servidor foi alterado. Tem de ser maior ou igual ao valor da Cópia de segurança mais antiga da base de dados de origem.
- Servidor de destino: Forneça um novo nome de servidor que pretende restaurar para
- Servidor de origem: Forneça o nome do servidor que pretende restaurar a partir de
- Localização: Não é possível selecionar a região, por predefinição é igual ao servidor de origem

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

O comando `az mariadb server restore` precisa dos seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos no qual se encontra o servidor de origem.  |
| nome | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selecione um ponto anterior no tempo para o qual restaurar. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seu fuso horário local, como `2017-04-13T05:59:00-08:00`, ou utilizar o formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | O nome ou ID do servidor de origem do qual pretende restaurar. |

Restaurar um servidor para um ponto anterior no tempo cria um novo servidor, copiado como o servidor original do ponto anterior no tempo que especificar. A localização e os valores do escalão de preço do servidor restaurado são os mesmos do servidor de origem.

O comando é síncrono e irá regressar depois de o servidor ser restaurado. Depois de o restauro ser concluído, localize o novo servidor que foi criado. Certifique-se de que os dados foram restaurados conforme esperado.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a:
> [!div class="checklist"]
> * Criar um Azure Database for MariaDB Server
> * Configurar a firewall do servidor
> * Utilize a [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados
