---
title: 'Tutorial: Aprovisionar uma base de dados do Azure para servidor MySQL com o modelo Azure Resource Manager'
description: Este tutorial explica como aprovisionar e automatizar a base de dados do Azure para implementações de servidor MySQL com o modelo Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: 8b443d0002b600860597710792054e9531b6cc41
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532917"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Tutorial: Aprovisionar uma base de dados do Azure para servidor MySQL com o modelo Azure Resource Manager

O [base de dados do Azure para MySQL REST API](https://docs.microsoft.com/rest/api/mysql/) permita aos engenheiros de DevOps automatizar e integrar o aprovisionamento, configuração e operações de servidores MySQL gerido e de bases de dados no Azure.  A API permite a criação, a enumeração, a gestão e a eliminação do MySQL servidores e bases de dados na base de dados do Azure para o serviço MySQL.

O Azure Resource Manager aproveitar a API REST para declarar e os recursos do Azure necessários para implementações em escala, alinhar com a infraestrutura como um conceito de código de programa subjacentes. O modelo parametriza a rede de nome, SKU, de recursos do Azure, configuração de firewall, e as definições, permitindo que ele seja criado um tempo e utilizada várias vezes.  Modelos Azure Resource Manager podem ser facilmente criados usando [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) ou [Visual Studio Code](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). Eles permitem a automação de implantação, que pode ser integrada no pipeline de DevOps CI/CD, padronização e empacotamento de aplicativos.  Por exemplo, se quiser para implementar rapidamente uma aplicação Web com a base de dados do Azure para o back-end MySQL, pode efetuar a implementação de ponto-a-ponto através desta [modelo de início rápido](https://azure.microsoft.com/en-us/resources/templates/101-webapp-managed-mysql/) da galeria do GitHub.

Neste tutorial, utilize modelo Azure Resource Manager e outros utilitários para saber como:

> [!div class="checklist"]
> * Criar uma base de dados do Azure para o servidor MySQL com o ponto final de serviço de VNet com o modelo Azure Resource Manager
> * Utilize a [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Criar uma base de dados do Azure para o servidor MySQL com o ponto final de serviço de VNet com o modelo Azure Resource Manager

Para obter a referência de modelo JSON para uma base de dados do Azure para o servidor MySQL, aceda a [Microsoft.DBforMySQL servidores](/azure/templates/microsoft.dbformysql/servers) referência de modelo. Segue-se o modelo de JSON de exemplo que pode ser utilizado para criar um novo servidor a executar a base de dados do Azure para MySQL com o ponto final de serviço da VNet.
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
Este pedido, os valores que precisam para serem personalizados são:
+   `name` -Especifique o nome do seu servidor MySQL (sem nome de domínio).
+   `location` -Especifique uma região do Centro de dados do Azure válida para o servidor MySQL. Por exemplo, westus2.
+   `properties/version` -Especifique a versão do servidor MySQL para implementar. Por exemplo, 5.6 ou 5.7.
+   `properties/administratorLogin` -Especifique o início de sessão do administrador do MySQL para o servidor. O nome de início de sessão de administrador não pode ser azure_superuser, administrador, administrador, raiz, convidados ou público.
+   `properties/administratorLoginPassword` -Especifique a palavra-passe para o utilizador de administrador do MySQL especificado acima.
+   `properties/sslEnforcement` -Especifique ativado/desativado para ativar/desativar sslEnforcement.
+   `storageProfile/storageMB` -Especifique o tamanho de armazenamento aprovisionado máximo necessário para o servidor em megabytes. Por exemplo, 5120.
+   `storageProfile/backupRetentionDays` -Especifique o período de retenção de cópia de segurança pretendido em dias. Por exemplo, 7. 
+   `storageProfile/geoRedundantBackup` -Especifique ativado/desativado dependendo dos requisitos de Geo-DR.
+   `sku/tier` -Especifique o escalão básico, GeneralPurpose ou MemoryOptimized para implementação.
+   `sku/capacity` -Especifique a capacidade de vCore. Os valores possíveis incluem 2, 4, 8, 16, 32 ou 64.
+   `sku/family` -Especifique Gen5 para escolher a geração de hardware para a implementação de servidor.
+   `sku/name` -Especifique TierPrefix_family_capacity. Por exemplo B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Consulte a [escalões de preço](./concepts-pricing-tiers.md) documentação para compreender os valores válidos por região e por camada.
+   `resources/properties/virtualNetworkSubnetId` -Especifique o identificador do Azure da sub-rede na VNet onde o servidor MySQL do Azure deve ser colocado. 
+   `tags(optional)` -Especifique etiquetas opcionais são pares chave-valor que pretende utilizar para categorizar os recursos para faturação etc.

Se estiver à procura para criar um modelo do Azure Resource Manager para automatizar a base de dados do Azure para MySQL implementações para a sua organização, a recomendação seria começar a partir do exemplo [modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) no Azure Galeria do GitHub de início rápido primeiro e crie com base no mesmo. 

Se estiver familiarizado com modelos Azure Resource Manager e gostaria de experimentá-la, pode iniciar, seguindo estes passos:
+   Clonar ou transferir o exemplo [modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) da Galeria de início rápido do Azure.  
+   Modifique o azuredeploy para atualizar os valores de parâmetro com base na sua preferência e guarde o ficheiro. 
+   Utilizar a CLI do Azure para criar o servidor MySQL do Azure com os comandos seguintes

Pode utilizar o Azure Cloud Shell no browser ou instalar a CLI do Azure no seu computador para executar os blocos de código neste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l “West US2”
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

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a:
> [!div class="checklist"]
> * Criar uma base de dados do Azure para o servidor MySQL com o ponto final de serviço de VNet com o modelo Azure Resource Manager
> * Utilize a [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados

> [Como ligar aplicações à Base de Dados do Azure para MySQL](./howto-connection-string.md)
