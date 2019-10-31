---
title: 'Tutorial: provisionar um banco de dados do Azure para servidor MySQL usando Azure Resource Manager modelo'
description: Este tutorial explica como provisionar e automatizar implantações do servidor de banco de dados do Azure para MySQL usando Azure Resource Manager modelo.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: 69025dd70ffe88c1592cf656e956b3e78a97a5e7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163898"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Tutorial: provisionar um banco de dados do Azure para servidor MySQL usando Azure Resource Manager modelo

A [API REST do banco de dados do Azure para MySQL](https://docs.microsoft.com/rest/api/mysql/) permite que os engenheiros de DevOps automatizem e integrem provisionamento, configuração e operações de servidores e bancos de dados MySQL gerenciados no Azure.  A API permite a criação, a enumeração, o gerenciamento e a exclusão de servidores MySQL e bancos de dados no serviço do Azure para MySQL.

Azure Resource Manager aproveitar a API REST subjacente para declarar e programar os recursos do Azure necessários para implantações em escala, alinhando-se com a infraestrutura como um conceito de código. O modelo parametriza o nome do recurso do Azure, a SKU, a rede, a configuração do firewall e as configurações, permitindo que ele seja criado uma vez e usado várias vezes.  Azure Resource Manager modelos podem ser facilmente criados usando [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) ou [Visual Studio Code](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). Eles habilitam o empacotamento de aplicativos, a padronização e a automação da implantação, que podem ser integrados ao pipeline de CI/CD do DevOps.  Por exemplo, se você estiver procurando implantar rapidamente um aplicativo Web com o back-end do banco de dados do Azure para MySQL, você pode executar a implantação de ponta a ponta usando este [modelo de início rápido](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) na galeria do github.

Neste tutorial, você usa Azure Resource Manager modelo e outros utilitários para aprender a:

> [!div class="checklist"]
> * Criar um servidor de banco de dados do Azure para MySQL com o ponto de extremidade de serviço VNet usando Azure Resource Manager modelo
> * Utilize a [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Criar um servidor de banco de dados do Azure para MySQL com o ponto de extremidade de serviço VNet usando Azure Resource Manager modelo

Para obter a referência de modelo JSON para um servidor de banco de dados do Azure para MySQL, vá para [Microsoft. DBforMySQL servidores](/azure/templates/microsoft.dbformysql/servers) de referência de modelo. Veja abaixo o modelo JSON de exemplo que pode ser usado para criar um novo servidor que executa o banco de dados do Azure para MySQL com o ponto de extremidade do serviço VNet.
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
Nessa solicitação, os valores que precisam ser personalizados são:
+   `name`-especifique o nome do seu servidor MySQL (sem nome de domínio).
+   `location`-especifique uma região de data center do Azure válida para o servidor MySQL. Por exemplo, westus2.
+   `properties/version`-especifique a versão do MySQL Server a ser implantada. Por exemplo, 5,6 ou 5,7.
+   `properties/administratorLogin`-especifique o logon de administrador do MySQL para o servidor. O nome de entrada do administrador não pode ser azure_superuser, admin, administrador, raiz, convidado ou público.
+   `properties/administratorLoginPassword`-especifique a senha para o usuário administrador do MySQL especificado acima.
+   `properties/sslEnforcement`-especifique habilitado/desabilitado para habilitar/desabilitar sslEnforcement.
+   `storageProfile/storageMB`-especifique o tamanho máximo de armazenamento provisionado necessário para o servidor em megabytes. Por exemplo, 5120.
+   `storageProfile/backupRetentionDays`-especifique o período de retenção de backup desejado em dias. Por exemplo, 7. 
+   `storageProfile/geoRedundantBackup`-especifique habilitado/desabilitado dependendo dos requisitos de DR-Rd.
+   `sku/tier`-especifique a camada básica, GeneralPurpose ou MemoryOptimized para implantação.
+   `sku/capacity`-especifique a capacidade vCore. Os valores possíveis incluem 2, 4, 8, 16, 32 ou 64.
+   `sku/family`-especifique Gen5 para escolher a geração de hardware para a implantação do servidor.
+   `sku/name`-especifique TierPrefix_family_capacity. Por exemplo, B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Consulte a documentação [tipos de preço](./concepts-pricing-tiers.md) para entender os valores válidos por região e por camada.
+   `resources/properties/virtualNetworkSubnetId`-especifique o identificador do Azure da sub-rede na VNet em que o servidor MySQL do Azure deve ser colocado. 
+   `tags(optional)`-especificar marcas opcionais são pares chave-valor que você usaria para categorizar os recursos de cobrança, etc.

Se você pretende criar um modelo de Azure Resource Manager para automatizar as implantações do banco de dados do Azure para MySQL para sua organização, a recomendação seria iniciar do [modelo de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) de exemplo na Galeria de início rápido do GitHub do Azure Primeiro, e crie sobre ele. 

Se você for novo no Azure Resource Manager modelos e quiser experimentá-lo, poderá começar seguindo estas etapas:
+   Clone ou baixe o [modelo de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) de exemplo da Galeria de início rápido do Azure.  
+   Modifique o azuredeploy. Parameters. JSON para atualizar os valores de parâmetro com base em sua preferência e salve o arquivo. 
+   Use CLI do Azure para criar o servidor MySQL do Azure usando os seguintes comandos

Você pode usar o Azure Cloud Shell no navegador ou instalar CLI do Azure em seu próprio computador para executar os blocos de código neste tutorial.

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
> * Criar um servidor de banco de dados do Azure para MySQL com o ponto de extremidade de serviço VNet usando Azure Resource Manager modelo
> * Utilize a [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> 
> [Como ligar aplicações à Base de Dados do Azure para MySQL](./howto-connection-string.md)
