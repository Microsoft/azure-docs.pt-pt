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
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: c7dd2fba852881680b43ceabafec2ac6b2751df4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839598"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Criar e gerir áreas de trabalho do serviço Azure Machine Learning

Neste artigo, vai criar, ver e eliminar [ **áreas de trabalho do serviço Azure Machine Learning** ](concept-azure-machine-learning-architecture.md#workspace) no portal do Azure para [serviço Azure Machine Learning](overview-what-is-azure-ml.md).  Também pode criar e eliminar as áreas de trabalho [com a CLI](reference-azure-machine-learning-cli.md) ou [com o código de Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Criar uma área de trabalho 

Para criar uma área de trabalho, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Ver uma área de trabalho

1. No canto superior esquerdo do portal, selecione **todos os serviços**. 

1. Na **todos os serviços** campo de filtro, escreva **área de trabalho do Machine Learning serviço**.  

   ![Procure a área de trabalho do serviço Azure Machine Learning](media/how-to-manage-workspace/allservices-search1.png)

1. Nos resultados de filtro, selecione **área de trabalho do Machine Learning serviço** para apresentar uma lista as áreas de trabalho. 

   ![Lista de áreas de trabalho do serviço Azure Machine Learning](media/how-to-manage-workspace/allservices-search.PNG)

1. Consultar a lista de áreas de trabalho foi encontrado. Pode filtrar com base na subscrição, grupos de recursos e localizações.  

   ![Áreas de trabalho do Vista](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Selecione a área de trabalho que acabou de criar para apresentar as respetivas propriedades.

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
