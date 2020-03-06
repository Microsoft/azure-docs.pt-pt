---
title: Quickstart - Crie um Azure Cosmos DB e um recipiente utilizando o modelo de Gestor de Recursos Azure
description: Quickstart mostrando como uma base de dados Azure Cosmos e um recipiente usando o modelo de Gestor de Recursos Azure
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.custom: subject-armqs
ms.openlocfilehash: 7c02cdf772b06f7070071aa7ba35c59b019187cc
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301731"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Quickstart: Crie um Azure Cosmos DB e um recipiente utilizando o modelo de Gestor de Recursos Azure

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode utilizar o Azure Cosmos DB para criar e consultar rapidamente bases de dados chave/valor, bases de dados de documentos e bases de dados de gráficos. Este quickstart centra-se no processo de implementação de um modelo de Gestor de Recursos para criar uma base de dados Azure Cosmos e um recipiente dentro dessa base de dados. Mais tarde, pode armazenar dados neste recipiente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição Azure ou conta experimental gratuita do Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Criar uma conta Azure Cosmos, base de dados, contentor

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

Três recursos Azure são definidos no modelo:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): Create a Azure Cosmos account.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Criar uma base de dados Azure Cosmos.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Create a Azure Cosmos container.

Mais amostras de modelo séc. [](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb)

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma conta Azure Cosmos, uma base de dados e um recipiente.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou introduza os seguintes valores.

   ![Modelo de Gestor de Recursos, integração de DB Azure Cosmos, portal de implantação](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    A menos que seja especificado, use os valores padrão para criar os recursos Azure Cosmos.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **Criar novo,** introduza um nome único para o grupo de recursos e, em seguida, clique **OK**.
    * **Localização**: selecione uma localização.  Por exemplo, **Centro dos EUA.**
    * **Nome da conta**: insira um nome para a conta Azure Cosmos. Deve ser globalmente único.
    * **Localização**: insira um local onde pretende criar a sua conta Azure Cosmos. A conta Azure Cosmos pode estar no mesmo local que o grupo de recursos.
    * **Região Primária**: A principal região de réplica para a conta Azure Cosmos.
    * **Região secundária**: A região de réplica secundária para a conta Azure Cosmos.
    * **Nome da base**de dados : O nome da base de dados Azure Cosmos.
    * **Nome**do contentor : O nome do recipiente Azure Cosmos.
    * **Entrada**: A entrada para o recipiente, o valor mínimo de entrada é de 400 RU/s.
    * **Aceito os termos e condições acima apresentados**: selecione.

3. Selecione **Comprar**. Depois de a conta Azure Cosmos ter sido implementada com sucesso, recebe uma notificação:

   ![Modelo de Gestor de Recursos, Integração Cosmos DB, notificação do portal de implementação](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

O portal Azure é usado para implantar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implantação](../azure-resource-manager/templates/deploy-powershell.md).

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

Se pretende continuar a trabalhar com tutoriais subsequentes e tutoriais, poderá desejar deixar esses recursos no lugar.
Quando já não for necessário, elimine o grupo de recursos, que elimina a conta Azure Cosmos e os recursos conexos. Para eliminar o grupo de recursos utilizando o Azure CLI ou o Azure Powershell:

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

Neste arranque rápido, criou uma conta Azure Cosmos, uma base de dados e um contentor utilizando um modelo de Gestor de Recursos Azure e validou a implementação. Para saber mais sobre o Azure Cosmos DB e o Gestor de Recursos Azure, continue para os artigos abaixo.

- Leia uma [visão geral do Azure Cosmos DB](introduction.md)
- Saiba mais sobre [o Gestor de Recursos Azure](../azure-resource-manager/management/overview.md)
- Obtenha outros modelos de Gestor de [Recursos DB Da Azure Cosmos](resource-manager-samples.md)