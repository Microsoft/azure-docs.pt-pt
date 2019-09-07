---
title: Criar espaços de trabalho do Azure ML no portal
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
ms.openlocfilehash: df015df3c20cdb99512167e3dc3e455da2762c52
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744790"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces-in-the-azure-portal"></a>Criar e gerenciar espaços de trabalho de serviço do Azure Machine Learning no portal do Azure

Neste artigo, vai criar, ver e eliminar [ **áreas de trabalho do serviço Azure Machine Learning** ](concept-workspace.md) no portal do Azure para [serviço Azure Machine Learning](overview-what-is-azure-ml.md).  O portal é a maneira mais fácil de começar com espaços de trabalho, mas conforme suas necessidades mudam ou requisitos para aumentar a automação, você também pode criar e excluir espaços de trabalho [usando a CLI](reference-azure-machine-learning-cli.md), [com código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou [por meio da extensão vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Para criar uma área de trabalho, precisa de uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Baixar um arquivo de configuração

1. Se você for criar uma [VM do bloco de anotações](tutorial-1st-experiment-sdk-setup.md#azure), ignore esta etapa.

1. Se você planeja usar o código em seu ambiente local que faz referência a esse espaço de trabalho, selecione **baixar config. JSON** na seção **visão geral** do espaço de trabalho.  

   ![Baixar config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Coloque o arquivo na estrutura de diretório com seus scripts Python ou Jupyter notebooks. Ele pode estar no mesmo diretório, um subdiretório chamado *. azureml*ou em um diretório pai. Quando você cria uma VM de notebook, esse arquivo é adicionado ao diretório correto na VM para você.


## <a name="view"></a>Exibir um espaço de trabalho

1. No canto superior esquerdo do portal, selecione **todos os serviços**.

1. No campo filtro **todos os serviços** , digite **serviço de Machine Learning**.  

1. Selecione **Machine Learning espaços de trabalho de serviço**.

   ![Procurar Azure Machine Learning espaço de trabalho do serviço](media/how-to-manage-workspace/all-services.png)

1. Consultar a lista de áreas de trabalho foi encontrado. Pode filtrar com base na subscrição, grupos de recursos e localizações.  

1. Selecione um espaço de trabalho para exibir suas propriedades.
   ![Propriedades do espaço de trabalho](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Utilize o botão de eliminação na parte superior da área de trabalho que pretende eliminar.

  ![Botão de eliminar](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Siga o tutorial completo para saber como utilizar uma área de trabalho para criar, preparar e implementar modelos com o serviço Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
