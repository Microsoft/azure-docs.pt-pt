---
title: 'Tutorial: Criar base de dados Azure para o mySQL - modelo de gestor de recursos Azure'
description: Este tutorial explica como providenciar e automatizar a Base de Dados Azure para implementações de servidores MySQL utilizando o modelo do Gestor de Recursos Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc
ms.openlocfilehash: a6923f0a1d568cc695b86d1538ba55a3eb3444da
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341489"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Tutorial: Fornecimento de uma base de dados Azure para servidor MySQL usando o modelo do Gestor de Recursos Azure

A [Base de Dados Azure para a API MySQL REST](/rest/api/mysql/) permite aos engenheiros da DevOps automatizar e integrar o fornecimento, configuração e operações de servidores e bases de dados geridos do MySQL em Azure.  A API permite a criação, enumeração, gestão e eliminação de servidores e bases de dados MySQL na Base de Dados Azure para o serviço MySQL.

O Azure Resource Manager aproveita a API rest subjacente para declarar e programar os recursos Azure necessários para implantações em escala, alinhando-se com a infraestrutura como um conceito de código. O modelo parametriza o nome de recurso Azure, SKU, rede, configuração de firewall e configurações, permitindo que seja criado uma vez e usado várias vezes.  Os modelos Azure Resource Manager podem ser facilmente criados usando [o portal Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) ou [o Código do Estúdio Visual.](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI) Permitem a embalagem de aplicações, normalização e automatização de implantação, que podem ser integradas no gasoduto DevOps CI/CD.  Por exemplo, se estiver a procurar implementar rapidamente uma Web App com base de dados Azure para o backend MySQL, pode executar a implementação de ponta a ponta utilizando este [modelo QuickStart](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) a partir da galeria GitHub.

Neste tutorial, você usa o modelo Azure Resource Manager e outros utilitários para aprender a:

> [!div class="checklist"]
> * Criar uma base de dados Azure para servidor MySQL com ponto final de serviço VNet usando o modelo do Gestor de Recursos Azure
> * Use [a ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Criar uma base de dados Azure para servidor MySQL com ponto final de serviço VNet usando o modelo do Gestor de Recursos Azure

Para obter a referência do modelo JSON para uma base de dados Azure para o servidor MySQL, vá à referência do modelo [de servidores microsoft.DBforMySQL.](/azure/templates/microsoft.dbformysql/servers) Abaixo está o modelo JSON da amostra que pode ser usado para criar um novo servidor que executa a Base de Dados Azure para o MySQL com o VNet Service Endpoint.
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
+   `name` - Especificar o nome do seu Servidor MySQL (sem nome de domínio).
+   `location` - Especifique uma região válida do centro de dados Azure para o seu Servidor MySQL. Por exemplo, westus2.
+   `properties/version` - Especificar a versão do servidor MySQL para implementar. Por exemplo, 5.6 ou 5.7.
+   `properties/administratorLogin` - Especificar o login de administração MySQL para o servidor. O nome de início de sessão de administrador não pode ser azure_superuser, admin, administrator, root, guest ou public.
+   `properties/administratorLoginPassword` - Especifique a palavra-passe para o utilizador de administração MySQL acima especificado.
+   `properties/sslEnforcement` - Especificar Ativado/Desativado para ativar/desativar o sslEnforcement.
+   `storageProfile/storageMB` - Especifique o tamanho máximo de armazenamento previsto para o servidor em megabytes. Por exemplo, 5120.
+   `storageProfile/backupRetentionDays` - Especificar o período de retenção de backup desejado em dias. Por exemplo, 7. 
+   `storageProfile/geoRedundantBackup` - Especificar Ativado/Desativado em função dos requisitos de Geo-DR.
+   `sku/tier` - Especificar o nível Básico, GeneralPurpose ou MemoryOptimed para implantação.
+   `sku/capacity` - Especificar a capacidade vCore. Os valores possíveis incluem 2, 4, 8, 16, 32 ou 64.
+   `sku/family` - Especifique a Gen5 para escolher a geração de hardware para a implementação do servidor.
+   `sku/name` - Especificar TierPrefix_family_capacity. Por exemplo, B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Consulte a documentação [dos níveis de preços](./concepts-pricing-tiers.md) para entender os valores válidos por região e por nível.
+   `resources/properties/virtualNetworkSubnetId` - Especificar o identificador Azure da sub-rede em VNet onde deve ser colocado o servidor Azure MySQL. 
+   `tags(optional)` - Especificar etiquetas opcionais são pares de valor chave que usaria para categorizar os recursos para faturação, etc.

Se procura construir um modelo de Gestor de Recursos Azure para automatizar a Base de Dados Azure para implementações do MySQL para a sua organização, a recomendação seria começar a partir do modelo de gestor [de recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) em Azure Quickstart GitHub Gallery primeiro e construir em cima dela. 

Se você é novo nos modelos do Azure Resource Manager e gostaria de experimentá-lo, você pode começar seguindo estes passos:
+   Clone ou descarregue o modelo sample [Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) da galeria Azure Quickstart.  
+   Modifique o azuredeploy.parameters.jspara atualizar os valores dos parâmetros com base na sua preferência e guarde o ficheiro. 
+   Utilize o CLI Azure para criar o servidor Azure MySQL utilizando os seguintes comandos

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

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com)procure e selecione **grupos de Recursos.**

2. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.

3. Na página **geral** do seu grupo de recursos, selecione **Delete resource group**.

4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a:
> [!div class="checklist"]
> * Criar uma base de dados Azure para servidor MySQL com ponto final de serviço VNet usando o modelo do Gestor de Recursos Azure
> * Utilizar a ferramenta de linha de comandos mysql para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados

> [!div class="nextstepaction"]
> [Como ligar aplicações à Base de Dados do Azure para MySQL](./howto-connection-string.md)