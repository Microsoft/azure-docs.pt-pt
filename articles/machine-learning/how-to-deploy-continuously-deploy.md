---
title: Implementar continuamente modelos de aprendizagem automática Azure
titleSuffix: Azure Machine Learning
description: Aprenda a implementar continuamente os modelos com a extensão Azure Machine Learning DevOps. Verifique automaticamente e implemente novas versões de modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: how-to, tracking-python, deploy
ms.openlocfilehash: 9de971639e22f9656ea75dc64993ac5881efbffb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609418"
---
# <a name="continuously-deploy-models"></a>Implementar modelos de forma contínua

Este artigo mostra como usar a implementação contínua em Azure DevOps para verificar automaticamente novas versões de modelos registados e empurrar esses novos modelos para a produção.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já registou um modelo no seu espaço de trabalho Azure Machine Learning. Consulte [este tutorial](how-to-train-scikit-learn.md) para um exemplo de formação e registo de um modelo de aprendizagem de scikit.

## <a name="continuously-deploy-models"></a>Implementar modelos de forma contínua

Pode implementar continuamente os modelos utilizando a extensão machine learning para [Azure DevOps](https://azure.microsoft.com/services/devops/). Pode utilizar a extensão machine learning para Azure DevOps para desencadear um oleoduto de implantação quando um novo modelo de aprendizagem automática estiver registado num espaço de trabalho de Aprendizagem automática Azure.

1. Inscreva-se nos [Pipelines Azure,](/azure/devops/pipelines/get-started/pipelines-sign-up)o que torna possível a integração contínua e entrega da sua aplicação em qualquer plataforma ou nuvem. (Note que os gasodutos Azure não são os mesmos que [os gasodutos de machine learning](concept-ml-pipelines.md#compare).)

1. [Criar um projeto Azure DevOps.](/azure/devops/organizations/projects/create-project)

1. Instale a [extensão machine learning para a Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Utilize ligações de serviço para configurar uma ligação principal de serviço ao seu espaço de trabalho Azure Machine Learning para que possa aceder aos seus artefactos. Vá para as definições do projeto, selecione **as ligações de serviço** e, em seguida, selecione **Azure Resource Manager**:

    [![Selecione Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na lista **de níveis scope,** selecione **AzureMLWorkspace** e, em seguida, introduza o resto dos valores:

    ![Selecione AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implementar continuamente o seu modelo de aprendizagem automática utilizando gasodutos Azure, sob oleodutos, selecione **o lançamento**. Adicione um novo artefacto e, em seguida, selecione o artefacto **do Modelo AzureML** e a ligação de serviço que criou anteriormente. Selecione o modelo e a versão para desencadear uma implementação:

    [![Selecione modelo AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Ative o gatilho do modelo no seu artefacto modelo. Quando liga o gatilho, sempre que a versão especificada (ou seja, a versão mais recente) desse modelo está registada no seu espaço de trabalho, é acionado um pipeline de libertação Azure DevOps.

    [![Ativar o gatilho do modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>Passos seguintes

Confira os projetos abaixo no GitHub para mais exemplos de implementação contínua para modelos ML.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)