---
title: Início rápido-criar um Azure Cosmos DB e um contêiner usando Azure Resource Manager modelo
description: Guia de início rápido mostrando como um banco de dados Cosmos do Azure e um contêiner usando Azure Resource Manager modelo
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 01/21/2020
ms.openlocfilehash: 12c2b0b089702b9e56ae099abbefd85769bc1d21
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549327"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Início rápido: criar um Azure Cosmos DB e um contêiner usando Azure Resource Manager modelo

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Você pode usar Azure Cosmos DB para criar e consultar rapidamente bancos de dados de chave/valor, bancos de dados de documentos e bancos de dados de grafo. Este guia de início rápido concentra-se no processo de implantação de um modelo do Resource Manager para criar um banco de dados Cosmos do Azure e um contêiner dentro desse banco de dados. Posteriormente, você poderá armazenar dados nesse contêiner.

O [modelo do Resource Manager](../azure-resource-manager/templates/overview.md) é um arquivo JavaScript Object Notation (JSON) que define a infraestrutura e a configuração do seu projeto. O modelo usa a sintaxe declarativa, que permite que você declare o que pretende implantar sem precisar escrever a sequência de comandos de programação para criá-lo. Se você quiser saber mais sobre como desenvolver modelos do Resource Manager, consulte a [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência do modelo](/azure/templates/microsoft.DocumentDB/allversions).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure ou uma conta de avaliação gratuita Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

## <a name="create-an-azure-cosmos-account-database-container"></a>Criar uma conta do Azure Cosmos, um banco de dados, um contêiner

O modelo usado neste guia de início rápido é de [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/).

[!code-json[<Resource Manager template create Azure Cosmos DB>](~/quickstart-templates/101-cosmosdb-create/azuredeploy.json)]

Três recursos do Azure são definidos no modelo:

* [Microsoft. DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): criar uma conta do Azure Cosmos.

* [Microsoft. DocumentDB/databaseAccounts/Sqldatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): criar um banco de dados Cosmos do Azure.

* [Microsoft. DocumentDB/databaseAccounts/Sqldatabases/contêineres](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): criar um contêiner Cosmos do Azure.

Mais exemplos de modelos de Azure Cosmos DB podem ser encontrados na [Galeria de modelos de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma conta do Azure Cosmos, um banco de dados e um contêiner.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou introduza os seguintes valores.

   ![Modelo do Resource Manager, integração de Azure Cosmos DB, implantar Portal](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    A menos que seja especificado, use os valores padrão para criar os recursos de Cosmos do Azure.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **criar novo**, insira um nome exclusivo para o grupo de recursos e clique em **OK**.
    * **Localização**: selecione uma localização.  Por exemplo, **E.U.A. Central**.
    * **Nome da conta**: Insira um nome para a conta do Azure Cosmos. Deve ser globalmente exclusivo. 
    * **Local**: Insira um local onde você deseja criar sua conta do Azure Cosmos. A conta do Azure Cosmos pode estar no mesmo local que o grupo de recursos. 
    * **Região primária**: a região da réplica primária para a conta do Azure Cosmos.
    * **Região secundária**: a região da réplica secundária para a conta do Azure Cosmos.
    * **Nome do banco de dados**: o nome do banco de dados Cosmos do Azure.
    * **Nome do contêiner**: o nome do contêiner Cosmos do Azure.
    * **Taxa**de transferência: a taxa de transferência para o contêiner, o valor de taxa de transferência mínima é de 400 ru/s. 
    * **Aceito os termos e condições acima apresentados**: selecione.

3. Selecione **Comprar**. Depois que a conta do Azure Cosmos tiver sido implantada com êxito, você receberá uma notificação:

   ![Modelo do Resource Manager, integração do Cosmos DB, implantar a notificação do portal](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, consulte [implantar modelos](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Você pode usar o portal do Azure para verificar a conta do Azure Cosmos, o banco de dados e o contêiner ou usar o CLI do Azure ou Azure PowerShell script a seguir para listar o segredo criado.

# <a name="clitabcli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name 
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar a trabalhar com os tutoriais e subseqüentes, talvez queira deixar esses recursos em vigor.
Quando não for mais necessário, exclua o grupo de recursos, que exclui a conta do Azure Cosmos e os recursos relacionados. Para excluir o grupo de recursos usando CLI do Azure ou o Azure PowerShell:

# <a name="clitabcli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou uma conta do Azure Cosmos, um banco de dados e um contêiner usando um modelo de Azure Resource Manager e validou a implantação. Para saber mais sobre Azure Cosmos DB e Azure Resource Manager, continue com os artigos abaixo.

- Leia uma [visão geral do Azure Cosmos DB](introduction.md)
- Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Obter outros [modelos do Azure Cosmos DB Resource Manager](resource-manager-samples.md)