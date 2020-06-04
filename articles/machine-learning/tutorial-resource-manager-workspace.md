---
title: Tutorial - Criar um espaço de trabalho Azure ML - Modelo de Gestor de Recursos
description: Neste tutorial, você usa um modelo de Gestor de Recursos Azure para implementar rapidamente um espaço de trabalho Azure para a aprendizagem automática
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 76f37beb22e28c0232efd0d62e82c8d3b60c78dc
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84345092"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>Tutorial: Implementar um espaço de trabalho de aprendizagem de máquinas Azure usando um modelo de Gestor de Recursos
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este tutorial irá mostrar-lhe como criar um espaço de trabalho de aprendizagem automática Azure usando um modelo de Gestor de Recursos Azure. Os espaços de trabalho de aprendizagem de máquinas Azure organizam todos os seus ativos de aprendizagem automática, desde conjuntos de dados de base até modelos implantados. Os espaços de trabalho são um único local para colaborar com os colegas na criação, execução e revisão de experiências, gerir a sua formação e inferenizar recursos compute, e monitorizar e versão de modelos implantados.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar

* Para utilizar os comandos CLI neste documento a partir do seu **ambiente local,** precisa do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json" range="1-258" highlight="224-254":::

Os seguintes recursos são definidos no modelo:

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Criar um espaço de trabalho Azure ML. Neste modelo, a localização e o nome são parâmetros que o utilizador pode passar ou introduzir interativamente.

### <a name="deploy-the-template"></a>Implementar o modelo 

Para utilizar o modelo a partir do CLI Azure, faça login e escolha a sua subscrição (Ver [Iniciar sessão com Azure CLI).](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) Em seguida, execute:

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

Quando executar o comando acima, insira:

1. Um nome de projeto que constituirá a base dos nomes do grupo de recursos criado e espaço de trabalho Azure ML
1. A localização Azure em que deseja fazer a implantação

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Para ver o seu espaço de trabalho Azure ML:

1. Ir para https://portal.azure.com 
1. Iniciar sessão 
1. Escolha o espaço de trabalho que acabou de criar

Você verá a página inicial do Azure Machine Learning: 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Screenshot do espaço de trabalho Azure ML":::

Para ver todos os recursos associados à implantação, clique no link na parte superior esquerda com o nome do espaço de trabalho (na imagem, `my_templated_ws` ). Esse link leva-o ao grupo de recursos do portal Azure. O nome do grupo de recursos é `{projectName}rg` e o espaço de trabalho é `{projectName}ws` nomeado.

## <a name="clean-up-resources"></a>Limpar recursos

Se não quiser utilizar este espaço de trabalho, elimine-o. Uma vez que o espaço de trabalho está associado a outros recursos, como uma conta de armazenamento, provavelmente irá querer apagar todo o grupo de recursos que criou. Pode eliminar o grupo de recursos utilizando o portal clicando no botão "Eliminar" e confirmando. Ou, pode eliminar o grupo de recursos do CLI com: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximos passos

Neste tutorial, criou um espaço de trabalho Azure Machine Learning a partir de um modelo de Gestor de Recursos Azure. Se quiser explorar a Azure Machine Learning, continue com o tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Começa a criar a tua primeira experiência de ML com o Python SDK](tutorial-1st-experiment-sdk-setup.md)
