---
title: Quickstart - Crie um Azure Cosmos DB e um recipiente utilizando o modelo de Gestor de Recursos Azure
description: Quickstart mostrando como uma base de dados Azure Cosmos e um recipiente usando o modelo de Gestor de Recursos Azure
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: b97702bb84eb38d185fe97ffcc596e374f28fbe2
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339670"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>Quickstart: Crie um Azure Cosmos DB e um recipiente usando um modelo ARM
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB é a base de dados NoSQL rápida da Microsoft com APIs abertos para qualquer escala. Pode utilizar o Azure Cosmos DB para criar e consultar rapidamente bases de dados chave/valor, bases de dados de documentos e bases de dados de gráficos. Este quickstart foca-se no processo de implantação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma base de dados Azure Cosmos e um contentor dentro dessa base de dados. Pode armazenar mais tarde os dados neste recipiente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Desdobre-se para Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição da Azure ou conta de teste gratuita da Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

Três recursos Azure são definidos no modelo:

* [Microsoft.DocumentDB/databaseSAcounts](/azure/templates/microsoft.documentdb/databaseaccounts): Criar uma conta Azure Cosmos.

* [Microsoft.DocumentDB/base de dadosAcons/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Criar uma base de dados Azure Cosmos.

* [Microsoft.DocumentDB/base de dadosAcons/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Criar um recipiente Azure Cosmos.

Mais amostras de modelo de Azure Cosmos DB podem ser encontradas na [galeria de modelos quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma conta Azure Cosmos, uma base de dados e um contentor.

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Desdobre-se para Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="Modelo ARM, integração DB Azure Cosmos, implementar portal":::

    A menos que seja especificado, use os valores padrão para criar os recursos Azure Cosmos.

    * **Subscrição** : selecione uma subscrição do Azure.
    * **Grupo de recursos** : selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, clique em **OK**.
    * **Localização** : selecione uma localização.  Por exemplo, **E.U.A. Central**.
    * **Nome da conta** : insira um nome para a conta Azure Cosmos. Deve ser globalmente único.
    * **Localização** : insira um local onde pretende criar a sua conta Azure Cosmos. A conta Azure Cosmos pode estar no mesmo local que o grupo de recursos.
    * **Região Primária** : A principal região réplica da conta Azure Cosmos.
    * **Região secundária** : A região de réplica secundária para a conta Azure Cosmos.
    * **Nível de consistência predefinido** : O nível de consistência predefinido para a conta Azure Cosmos.
    * **Prefixo Max Staleness** : Pedidos max. Obrigatório para a BoundedStaleness.
    * **Intervalo máximo em segundos:** tempo de desfasamento máximo. Obrigatório para a BoundedStaleness.
    * **Nome da base de dados** : O nome da base de dados Azure Cosmos.
    * **Nome do recipiente** : O nome do recipiente Azure Cosmos.
    * **Produção** : A produção para o contentor, o valor mínimo de produção é de 400 RU/s.
    * **Aceito os termos e condições acima apresentados** : selecione.

3. Selecione **Comprar**. Depois de a conta Azure Cosmos ter sido implantada com sucesso, recebe uma notificação:

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="Modelo ARM, integração de Cosmos DB, implementar notificação do portal":::

O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Pode utilizar o portal Azure para verificar a conta Azure Cosmos, a base de dados e o recipiente ou utilizar o seguinte script Azure CLI ou Azure PowerShell para listar o segredo criado.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia continuar a trabalhar com os rápidos e tutoriais subsequentes, talvez queira deixar estes recursos no lugar.
Quando já não for necessário, elimine o grupo de recursos, que elimina a conta Azure Cosmos e os recursos conexos. Para eliminar o grupo de recursos utilizando a Azure CLI ou a Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma conta Azure Cosmos, uma base de dados e um recipiente usando um modelo ARM e validou a implementação. Para saber mais sobre Azure Cosmos DB e Azure Resource Manager, continue para os artigos abaixo.

- Leia uma [visão geral de Azure Cosmos DB](introduction.md)
- Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Obtenha outros [modelos de Gestor de Recursos DB da Azure Cosmos](./templates-samples-sql.md)