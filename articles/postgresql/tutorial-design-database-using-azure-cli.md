---
title: 'Tutorial: Desenhe uma base de dados Azure para PostgreSQL - Servidor Único - Azure CLI'
description: Este tutorial mostra como criar, configurar e consultar a sua primeira Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: c1ab057c34a89bfa39a298805216af89b2327622
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500781"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tutorial: Desenhe uma base de dados Azure para PostgreSQL - Servidor Único usando Azure CLI 
Neste tutorial, utiliza a CLI do Azure (interface de linha de comandos) e outros utilitários para saber como:
> [!div class="checklist"]
> * Criar um servidor da Base de Dados do Azure para PostgreSQL
> * Configurar a firewall do servidor
> * Utilize o utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Pode utilizar o Azure Cloud Shell no browser ou [instalar a CLI do Azure]( /cli/azure/install-azure-cli) no computador para executar os comandos deste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso existe ou é cobrado. Selecione um ID de subscrição específica na sua conta com o comando [az account set](/cli/azure/account).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL
Crie uma [Base de Dados do Azure para o servidor PostgreSQL](overview.md) com o comando [az postgres server create](/cli/azure/postgres/server). Os servidores contêm um grupo de bases de dados geridas como um grupo. 

O exemplo seguinte cria um servidor com o nome `mydemoserver` no seu grupo de recursos `myresourcegroup` com o início de sessão `myadmin` de administrador do servidor. O nome de um servidor mapeia para o nome DNS e, por conseguinte, é necessário para ser globalmente exclusivo no Azure. Substitua `<server_admin_password>` pelo seu próprio valor. É um servidor De Propósito Geral, Gen 5 com 2 vCores.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
O valor do parâmetro sku-name segue a convenção {escalão de preço} \_ {geração de computação} \_ {vCores}, conforme os exemplos abaixo:
+ `--sku-name B_Gen5_2`mapas para Basic, Gen 5 e 2 vCores.
+ `--sku-name GP_Gen5_32` mapeia para Fins Gerais, Ger 5 e 32 vCores.
+ `--sku-name MO_Gen5_2` mapeia para Otimizada para Memória, Ger 5 e 2 vCores.

Leia a documentação dos [escalões de preços](./concepts-pricing-tiers.md) para entender os valores válidos por região e por escalão.

> [!IMPORTANT]
> O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste início rápido. Lembre-se ou grave estas informações para utilização posterior.

Por predefinição, é criada a base de dados **postgres** no servidor. A base de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é uma base de dados predefinida que se destina a ser utilizada por utilizadores, utilitários e aplicações de terceiros. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Crie uma regra de firewall ao nível do servidor do Azure PostgreSQL com o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). A regra de firewall ao nível do servidor permite que uma aplicação externa, como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), se ligue ao seu servidor através da firewall do serviço Azure PostgreSQL. 

Pode definir uma regra de firewall que abrange um intervalo IP para conseguir estabelecer ligação a partir da sua rede. O exemplo seguinte utiliza [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) para criar uma regra de firewall `AllowMyIP` que permite ligações de um endereço IP único.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Para restringir o acesso ao seu servidor PostgreSQL do Azure apenas à sua rede, pode definir a regra de firewall para que abranja apenas o intervalo de endereços IP da sua rede empresarial.

> [!NOTE]
> O servidor PostgreSQL do Azure comunica através da porta 5432. Ao ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Peça ao departamento de TI para abrir a porta 5432, para ligar ao servidor da Base de Dados SQL do Azure.
>

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

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Ligar à Base de Dados do Azure para PostgreSQL com psql
Se o seu computador cliente tiver o PostgreSQL instalado, pode utilizar uma instância local de [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) ou o Azure Cloud Console para ligar a um servidor PostgreSQL do Azure. Agora, vamos utilizar o utilitário da linha de comandos psql para ligar à Base de Dados do Azure para o servidor PostgreSQL.

1. Execute o comando psql seguinte para ligar a uma Base de Dados do Azure para PostgreSQL:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Por exemplo, o comando seguinte liga à base de dados predefinida com o nome **postgres** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** com as credenciais de acesso. Introduza o `<server_admin_password>` que escolheu quando lhe for pedida a palavra-passe.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se preferir utilizar um caminho URL para ligar aos Postgres, o URL codifica o sinal @ no nome de utilizador com `%40` . Por exemplo, a cadeia de ligação para psql seria,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Assim que estiver ligado ao servidor, crie uma base de dados em branco na linha de comandos:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** criada recentemente:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados
Agora que sabe como ligar à Base de Dados do Azure para PostgreSQL, pode concluir algumas tarefas básicas:

Em primeiro lugar, crie uma tabela e carregue-a com alguns dados. Por exemplo, vamos criar uma tabela que controla as informações de inventário:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Agora pode ver a tabela criada recentemente na lista de tabelas ao escrever:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Carregar dados para a tabela
Agora que já criámos uma tabela, insira alguns dados. Na janela da linha de comandos aberta, execute a seguinte consulta para inserir algumas linhas de dados:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Acabou de adicionar duas linhas de dados de exemplo às tabelas que criou anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para obter informações da tabela de inventário: 
```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados na tabela de inventário:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Pode ver os valores atualizados ao obter os dados:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo
Imagine que eliminou acidentalmente uma tabela. É algo de que não é fácil recuperar. A Base de Dados do Azure para PostgreSQL permite-lhe regressar a qualquer ponto anterior no tempo para o qual o seu servidor tenha cópias de segurança (determinado pelo período de retenção da cópia de segurança configurado por si) e restaurar este ponto anterior no tempo num novo servidor. Pode utilizar este servidor novo para recuperar os dados eliminados. 

O comando seguinte restaura o servidor de exemplo para um ponto antes de a tabela ter sido adicionada:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

O comando `az postgres server restore` precisa dos seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos no qual se encontra o servidor de origem.  |
| name | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selecione um ponto anterior no tempo para o qual restaurar. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seu fuso horário local, como `2017-04-13T05:59:00-08:00`, ou utilizar o formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | O nome ou ID do servidor de origem do qual pretende restaurar. |

Restaurar um servidor para um ponto anterior no tempo cria um novo servidor, copiado como o servidor original do ponto anterior no tempo que especificar. A localização e os valores do escalão de preço do servidor restaurado são os mesmos do servidor de origem.

O comando é síncrono e irá regressar depois de o servidor ser restaurado. Depois de o restauro ser concluído, localize o novo servidor que foi criado. Certifique-se de que os dados foram restaurados conforme esperado.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a utilizar a CLI (interface de linha de comandos) do Azure e outros utilitários para:
> [!div class="checklist"]
> * Criar um servidor da Base de Dados do Azure para PostgreSQL
> * Configurar a firewall do servidor
> * Utilize o utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Em seguida, aprenda a utilizar o portal do Azure para realizar tarefas semelhantes, reveja este tutorial: [Conceber a sua primeira Base de Dados do Azure para PostgreSQL com o portal do Azure](tutorial-design-database-using-azure-portal.md)
