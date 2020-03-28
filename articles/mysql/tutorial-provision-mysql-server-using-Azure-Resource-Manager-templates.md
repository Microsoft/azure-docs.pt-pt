---
title: 'Tutorial: Criar base de dados azure para o mySQL - modelo de Gestor de Recursos Azure'
description: Este tutorial explica como fornecer e automatizar a Base de Dados Azure para implementações de servidores MySQL usando o modelo de Gestor de Recursos Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc
ms.openlocfilehash: f4960482c88bf9768be1c1c9dbb3652409a8f1b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771105"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Tutorial: Fornecer uma base de dados Azure para servidor MySQL usando o modelo de Gestor de Recursos Azure

A Base de [Dados Azure para MySQL REST API](https://docs.microsoft.com/rest/api/mysql/) permite aos engenheiros da DevOps automatizar e integrar o fornecimento, configuração e operações de servidores e bases de dados mysqL geridos em Azure.  A API permite a criação, enumeração, gestão e eliminação de servidores MySQL e bases de dados na Base de Dados Azure para o serviço MySQL.

O Gestor de Recursos Azure aproveita a API subjacente ao REST para declarar e programar os recursos Azure necessários para implementações em escala, alinhando-se com a infraestrutura como um conceito de código. O modelo parametia o nome de recurso Azure, SKU, rede, configuração de firewall e configurações, permitindo que seja criado uma vez e usado várias vezes.  Os modelos do Gestor de Recursos Azure podem ser facilmente criados usando o [portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) ou [o Código do Estúdio Visual](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). Permitem a embalagem de aplicações, a normalização e a automatização de implantação, que podem ser integradas no pipeline CI/CD da DevOps.  Por exemplo, se procura implementar rapidamente uma Aplicação Web com base de dados Azure para o backend MySQL, pode realizar a implementação de ponta a ponta utilizando este [modelo QuickStart](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) a partir da galeria GitHub.

Neste tutorial, você usa o modelo do Gestor de Recursos Azure e outros utilitários para aprender a:

> [!div class="checklist"]
> * Crie uma base de dados Azure para servidor MySQL com endpoint de serviço VNet usando o modelo de Gestor de Recursos Azure
> * Use [a ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Crie uma base de dados Azure para servidor MySQL com endpoint de serviço VNet usando o modelo de Gestor de Recursos Azure

Para obter a referência do modelo JSON para uma Base de Dados Azure para servidor MySQL, vá à referência do modelo de [servidores Microsoft.DBforMySQL.](/azure/templates/microsoft.dbformysql/servers) Abaixo está o modelo JSON da amostra que pode ser usado para criar um novo servidor executando base de dados Azure para MySQL com VNet Service Endpoint.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
Neste pedido, os valores que precisam de ser personalizados são:
+   `name`- Especifique o nome do seu Servidor MySQL (sem nome de domínio).
+   `location`- Especifique uma região de centro de dados Azure válida para o seu Servidor MySQL. Por exemplo, westus2.
+   `properties/version`- Especificar a versão do servidor MySQL para implementar. Por exemplo, 5.6 ou 5.7.
+   `properties/administratorLogin`- Especifique o login de administrador mySQL para o servidor. O nome de início de sessão de administrador não pode ser azure_superuser, admin, administrator, root, guest ou public.
+   `properties/administratorLoginPassword`- Especificar a palavra-passe para o utilizador administrativo MySQL acima especificado.
+   `properties/sslEnforcement`- Especificar ativado/desativado para ativar/desativar o sslEnforcement.
+   `storageProfile/storageMB`- Especifique o tamanho máximo de armazenamento necessário para o servidor em megabytes. Por exemplo, 5120.
+   `storageProfile/backupRetentionDays`- Especifique o período de retenção de reserva desejado em dias. Por exemplo, 7. 
+   `storageProfile/geoRedundantBackup`- Especificar Ativado/Incapacitado em função dos requisitos de Geo-DR.
+   `sku/tier`- Especificar O nível Basic, GeneralPurpose ou MemoryOptimized para implementação.
+   `sku/capacity`- Especificar a capacidade vCore. Os valores possíveis incluem 2, 4, 8, 16, 32 ou 64.
+   `sku/family`- Especifique a Gen5 para escolher a geração de hardware para a implementação do servidor.
+   `sku/name`- Especifique TierPrefix_family_capacity. Por exemplo, B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Consulte a documentação dos níveis de preços para compreender os [valores válidos](./concepts-pricing-tiers.md) por região e por nível.
+   `resources/properties/virtualNetworkSubnetId`- Especifique o identificador Azure da subnet na VNet onde deve ser colocado o servidor Azure MySQL. 
+   `tags(optional)`- Especificar etiquetas opcionais são pares de valor-chave que utilizaria para categorizar os recursos para faturação, etc.

Se procura construir um modelo de Gestor de Recursos Azure para automatizar a Base de Dados Azure para implementações MySQL para a sua organização, a recomendação seria começar a partir do modelo de gestor de [recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) na Galeria Azure Quickstart GitHub primeiro e construir em cima dela. 

Se você é novo em modelos de Gestor de Recursos Azure e gostaria de experimentá-lo, você pode começar seguindo estes passos:
+   Clone ou baixe o modelo sample [Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) da galeria Azure Quickstart.  
+   Modifique o azuredeploy.parâmetros.json para atualizar os valores do parâmetro com base na sua preferência e guardar o ficheiro. 
+   Utilize o Azure CLI para criar o servidor Azure MySQL utilizando os seguintes comandos

Pode utilizar o Azure Cloud Shell no browser ou instalar a CLI do Azure no computador para executar os blocos de código deste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Obter as informações da ligação
Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Aponte o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
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

## <a name="connect-to-the-server-using-mysql"></a>Ligar ao servidor com o mysql
Utilize a [ferramenta de linha de comandos do mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para ligar à Base de Dados do Azure para o servidor MySQL. Neste exemplo, o comando é:
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
Agora que sabe como ligar à Base de Dados do Azure para MySQL, conclua algumas tarefas básicas.

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

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a:
> [!div class="checklist"]
> * Crie uma base de dados Azure para servidor MySQL com endpoint de serviço VNet usando o modelo de Gestor de Recursos Azure
> * Use [a ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> 
> [Como ligar aplicações à Base de Dados do Azure para MySQL](./howto-connection-string.md)
