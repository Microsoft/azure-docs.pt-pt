---
title: Criar espaços de trabalho do Azure ML no portal
titleSuffix: Azure Machine Learning
description: Saiba como criar, exibir e excluir espaços de trabalho do Azure Machine Learning no portal do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 9e3915a2f55680fc2e24ac63d5e7170ada026c2e
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615192"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Criar e gerenciar espaços de trabalho de Azure Machine Learning no portal do Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você criará, exibirá e excluirá [**espaços de trabalho Azure Machine Learning**](concept-workspace.md) no portal do Azure para [Azure Machine Learning](overview-what-is-azure-ml.md).  O portal é a maneira mais fácil de começar com espaços de trabalho, mas conforme suas necessidades mudam ou requisitos para aumentar a automação, você também pode criar e excluir espaços de trabalho [usando a CLI](reference-azure-machine-learning-cli.md), [com código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou [por meio da extensão vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Para criar um espaço de trabalho, você precisa de uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

1. Entre no [portal do Azure](https://portal.azure.com/) usando as credenciais para sua assinatura do Azure. 

1. No canto superior esquerdo da portal do Azure, selecione **+ criar um recurso**.

      ![Criar um novo recurso](../../../includes/media/aml-create-in-portal/create-workspace.gif)

1. Use a barra de pesquisa para localizar **Machine Learning**.

1. Selecione **Machine Learning**.

1. No painel **Machine Learning** , selecione **criar** para começar.

1. Forneça as seguintes informações para configurar seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome do espaço de trabalho |Insira um nome exclusivo que identifique seu espaço de trabalho. Neste exemplo, usamos **docs-WS**. Os nomes devem ser exclusivos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferencie os espaços de trabalho criados por outras pessoas.  
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Use um grupo de recursos existente em sua assinatura ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos contém recursos relacionados para uma solução do Azure. Neste exemplo, usamos **docs-AML**. 
   Localização | Selecione o local mais próximo de seus usuários e os recursos de dados para criar seu espaço de trabalho.
   Edição do espaço de trabalho | Selecione **básico** ou **Enterprise**.  Essa edição de espaço de trabalho determina os recursos aos quais você terá acesso e preços. Saiba mais sobre as [ofertas Basic e Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Configurar seu espaço de trabalho](media/how-to-manage-workspace/select-edition.png)

1. Depois de concluir a configuração do espaço de trabalho, selecione **criar**. 

   > [!Warning] 
   > Pode levar vários minutos para criar seu espaço de trabalho na nuvem.

   Quando o processo for concluído, uma mensagem de êxito de implantação será exibida. 
 
 1. Para exibir o novo espaço de trabalho, selecione **ir para o recurso**.

### <a name="download-a-configuration-file"></a>Baixar um arquivo de configuração

1. Se você for criar uma [VM do bloco de anotações](tutorial-1st-experiment-sdk-setup.md#azure), ignore esta etapa.

1. Se você planeja usar o código em seu ambiente local que faz referência a esse espaço de trabalho, selecione **baixar config. JSON** na seção **visão geral** do espaço de trabalho.  

   ![Baixar config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Coloque o arquivo na estrutura de diretório com seus scripts Python ou Jupyter notebooks. Ele pode estar no mesmo diretório, um subdiretório chamado *. azureml*ou em um diretório pai. Quando você cria uma VM de notebook, esse arquivo é adicionado ao diretório correto na VM para você.

## <a name="upgrade"></a>Atualizar para o Enterprise Edition

Você pode atualizar seu espaço de trabalho de edição básica para Enterprise Edition para aproveitar os recursos avançados, como experiências de código baixo e recursos de segurança aprimorados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o espaço de trabalho que você deseja atualizar.

1. Selecione **Atualizar** na parte superior ou na mensagem de atualização.

    ![Atualizar um espaço de trabalho](media/how-to-manage-workspace/upgrade.png)

1. Selecione **confirmar atualização**.


> [!IMPORTANT]
> Não é possível fazer downgrade de um espaço de trabalho Enterprise Edition para um espaço de trabalho básico. 

## <a name="view"></a>Localizar um espaço de trabalho

1. No campo de pesquisa superior, digite **Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Procurar Azure Machine Learning espaço de trabalho](media/how-to-manage-workspace/find-workspaces.png)

1. Veja a lista de espaços de trabalho encontrados. Você pode filtrar com base na assinatura, nos grupos de recursos e nos locais.  

1. Selecione um espaço de trabalho para exibir suas propriedades.

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Use o botão excluir na parte superior do espaço de trabalho que você deseja excluir.

  ![Botão excluir](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Siga o tutorial de tamanho completo para aprender a usar um espaço de trabalho para criar, treinar e implantar modelos com o Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: treinar modelos](tutorial-train-models-with-aml.md)
