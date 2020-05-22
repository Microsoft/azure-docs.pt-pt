---
title: Tutorial - Criar um espaço de trabalho Azure ML - Modelo de Gestor de Recursos
description: Neste tutorial, você usa um modelo de Gestor de Recursos Azure para implantar rapidamente um espaço de trabalho Azure para aprendizagem automática
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: b6eac711ed6c420330b036cb6094c841508cee9f
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791580"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>Tutorial: Implementar um espaço de trabalho de aprendizagem automática Azure usando um modelo de Gestor de Recursos
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este tutorial irá mostrar-lhe como criar um espaço de trabalho de aprendizagem automática Azure usando um modelo de Gestor de Recursos Azure. Os espaços de trabalho de aprendizagem automática Azure organizam todos os seus ativos de aprendizagem automática desde conjuntos de dados de base até modelos implantados. Os espaços de trabalho são um único local para colaborar com os colegas na criação, execução e revisão de experiências, gerir os seus recursos computacionais de formação e inferência, e monitorizar e versões modelos implantados.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar

* Para utilizar os comandos CLI neste documento a partir do seu **ambiente local,** você precisa do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

Os seguintes recursos são definidos no modelo:

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Criar um espaço de trabalho Azure ML. Neste modelo, a localização e o nome são parâmetros que o utilizador pode passar ou entrar interativamente.

### <a name="deploy-the-template"></a>Implementar o modelo 

Para utilizar o modelo do Azure CLI, inicie sessão e escolha a sua subscrição (Ver [Iniciar sessão com o Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Em seguida, execute:

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

Quando executar o comando acima, introduza:

1. Um nome de projeto que formará a base dos nomes do grupo de recursos criados e do espaço de trabalho Azure ML
1. A localização Azure na qual deseja emissão

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Para ver o seu espaço de trabalho Azure ML:

1. Ir para https://portal.azure.com 
1. Iniciar sessão 
1. Escolha o espaço de trabalho que acabou de criar

Verá a página inicial do Azure Machine Learning: 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Screenshot do espaço de trabalho Azure ML":::

Para ver todos os recursos associados à implementação, clique no link na parte superior esquerda com o nome do espaço de trabalho (na imagem, `my_templated_ws` ). Essa ligação leva-o ao grupo de recursos no portal Azure. O nome do grupo de recursos é `{projectName}rg` e o espaço de trabalho é nomeado `{projectName}ws` .

## <a name="clean-up-resources"></a>Limpar recursos

Se não quiser utilizar este espaço de trabalho, apague-o. Uma vez que o espaço de trabalho está associado a outros recursos, como uma conta de armazenamento, provavelmente vai querer eliminar todo o grupo de recursos que criou. Pode eliminar o grupo de recursos utilizando o portal clicando no botão "Eliminar" e confirmando. Ou, pode eliminar o grupo de recursos do CLI com: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um espaço de trabalho azure machine learning a partir de um modelo de Gestor de Recursos Azure. Se quiser explorar o Azure Machine Learning, continue com o tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Começar a criar a sua primeira experiência ML com o Python SDK](tutorial-1st-experiment-sdk-setup.md)
