---
title: Criar e gerir áreas de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como criar, ver e eliminar as áreas de trabalho do Azure Machine Learning serviço no portal do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 7f0806a1d68cd2cede1ae51f0a50a8125c1e7c8b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "66016543"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Criar e gerir áreas de trabalho do serviço Azure Machine Learning

Neste artigo, vai criar, ver e eliminar [ **áreas de trabalho do serviço Azure Machine Learning** ](concept-workspace.md) no portal do Azure para [serviço Azure Machine Learning](overview-what-is-azure-ml.md).  Também pode criar e eliminar as áreas de trabalho [com a CLI](reference-azure-machine-learning-cli.md), [com o código de Python](https://aka.ms/aml-sdk) ou [através da extensão do VS Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Para criar uma área de trabalho, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Ver uma área de trabalho

1. No canto superior esquerdo do portal, selecione **todos os serviços**.

1. Na **todos os serviços** campo de filtro, escreva **do machine learning serviço**.  

1. Selecione **áreas de trabalho do Machine Learning serviço**.

   ![Procure a área de trabalho do serviço Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Consultar a lista de áreas de trabalho foi encontrado. Pode filtrar com base na subscrição, grupos de recursos e localizações.  

1. Selecione uma área de trabalho para apresentar as respetivas propriedades.
   ![Propriedades da área de trabalho](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Utilize o botão de eliminação na parte superior da área de trabalho que pretende eliminar.

  ![Botão de eliminar](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Siga o tutorial completo para saber como utilizar uma área de trabalho para criar, preparar e implementar modelos com o serviço Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Preparação de modelos](tutorial-train-models-with-aml.md)
