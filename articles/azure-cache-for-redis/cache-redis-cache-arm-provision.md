---
title: Implemente cache Azure para Redis usando o modelo de gestor de recursos Azure
description: Aprenda a usar um modelo de Gestor de Recursos Azure (modelo ARM) para implementar uma Cache Azure para recurso Redis. Os modelos são fornecidos para cenários comuns.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 0445aeaea6f99754469d5c0e46972aef2ed667aa
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424208"
---
# <a name="create-an-azure-cache-for-redis-using-an-arm-template"></a>Crie uma cache Azure para redis usando um modelo ARM

Saiba como criar um modelo de Gestor de Recursos Azure (modelo ARM) que implementa uma Cache Azure para Redis. A cache pode ser usada com uma conta de armazenamento existente para manter os dados de diagnóstico. Também aprende a definir quais os recursos que são implantados e como definir parâmetros especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Atualmente, as definições de diagnóstico são partilhadas para todos os caches da mesma região para uma subscrição. A atualização de uma cache na região afeta todos os outros caches da região.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Uma conta de armazenamento**: Para criar uma, consulte [criar uma conta de Armazenamento Azure](/azure/storage/common/storage-account-create?tabs=azure-portal). A conta de armazenamento é utilizada para dados de diagnóstico.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

Os seguintes recursos são definidos no modelo:

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnósticos](/azure/templates/microsoft.insights/diagnosticsettings)

Os modelos de Gestor de Recursos para o novo [nível Premium](cache-overview.md#service-tiers) também estão disponíveis.

* [Criar uma Cache Premium Azure para Redis com agrupamento](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Criar Cache Premium Azure para Redis com persistência de dados](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Criar Cache Premium Redis implantado numa Rede Virtual](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Para verificar os modelos mais recentes, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/) e procure _por Azure Cache for Redis_.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a seguinte imagem para iniciar seduca e abrir o modelo.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Selecione ou introduza os seguintes valores:

    * **Subscrição**: selecione uma subscrição Azure usada para criar a partilha de dados e os outros recursos.
    * **Grupo de recursos**: selecione **Criar novos** para criar um novo grupo de recursos ou selecionar um grupo de recursos existente.
    * **Localização**: selecione uma localização para o grupo de recursos. A conta de armazenamento e a cache Redis devem estar na mesma região. Por predefinição, a cache Redis utiliza a mesma localização que o grupo de recursos. Então, especifique o mesmo local que a conta de armazenamento.
    * **Nome redis Cache**: insira um nome para a cache Redis.
    * **Conta de Armazenamento de Diagnósticos Existente:** introduza o ID de recurso de uma conta de armazenamento. A sintaxe é `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Utilize o valor predefinido para o resto das definições.
1. selecionar **Concordo com os termos e condições acima indicados,** e com a **aquisição**selecionada .

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra a cache Redis que criou.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um modelo de Gestor de Recursos Azure que implementa uma Cache Azure para Redis. Para aprender a criar um modelo de Gestor de Recursos Azure que implementa uma App Azure Web com Cache Azure para Redis, consulte [Criar uma Web App plus Azure Cache para Redis usando um modelo](./cache-web-app-arm-with-redis-cache-provision.md).
