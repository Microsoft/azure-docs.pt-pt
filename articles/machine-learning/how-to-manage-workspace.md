---
title: Criar Azure Machine Learning espaços de trabalho no portal
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
ms.openlocfilehash: d5f76bc0a48099ffebc3bc398ce62c74769ed52c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867124"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Criar e gerenciar espaços de trabalho de Azure Machine Learning no portal do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você criará, exibirá e excluirá [**espaços de trabalho Azure Machine Learning**](concept-workspace.md) no portal do Azure para [Azure Machine Learning](overview-what-is-azure-ml.md).  O portal é a maneira mais fácil de começar com espaços de trabalho, mas conforme suas necessidades mudam ou requisitos para aumentar a automação, você também pode criar e excluir espaços de trabalho [usando a CLI](reference-azure-machine-learning-cli.md), [com código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou [por meio da extensão vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Criar áreas de trabalho

Para criar uma área de trabalho, precisa de uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

1. Entre no [portal do Azure](https://portal.azure.com/) usando as credenciais para sua assinatura do Azure. 

1. No canto superior esquerdo da portal do Azure, selecione **+ criar um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Use a barra de pesquisa para localizar **Machine Learning**.

1. Selecione **Machine Learning**.

1. No painel **Machine Learning** , selecione **criar** para começar.

1. Forneça as seguintes informações para configurar seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome da área de trabalho |Insira um nome exclusivo que identifique seu espaço de trabalho. Neste exemplo, usamos **docs-WS**. Os nomes devem ser exclusivos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferencie os espaços de trabalho criados por outras pessoas.  
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Utilize um grupo de recursos já existente na sua subscrição ou introduza um nome para criar um grupo de recursos novo. Um grupo de recursos contém recursos relacionados para uma solução do Azure. Neste exemplo, usamos **docs-AML**. 
   Localização | Selecione o local mais próximo de seus usuários e os recursos de dados para criar seu espaço de trabalho.
   Edição do espaço de trabalho | Selecione **básico** ou **Enterprise**.  Essa edição de espaço de trabalho determina os recursos aos quais você terá acesso e preços. Saiba mais sobre as [ofertas Basic e Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Configurar seu espaço de trabalho](./media/how-to-manage-workspace/select-edition.png)

1. Depois de concluir a configuração do espaço de trabalho, selecione **criar**. 

   > [!Warning] 
   > Pode levar vários minutos para criar seu espaço de trabalho na nuvem.

   Quando o processo for concluído, uma mensagem de êxito de implantação será exibida. 
 
 1. Para exibir o novo espaço de trabalho, selecione **ir para o recurso**.

### <a name="download-a-configuration-file"></a>Baixar um arquivo de configuração

1. Se você for criar uma [instância de computação](tutorial-1st-experiment-sdk-setup.md#azure), ignore esta etapa.

1. Se você planeja usar o código em seu ambiente local que faz referência a esse espaço de trabalho, selecione **baixar config. JSON** na seção **visão geral** do espaço de trabalho.  

   ![Baixar config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Coloque o arquivo na estrutura de diretório com seus scripts Python ou Jupyter notebooks. Ele pode estar no mesmo diretório, um subdiretório chamado *. azureml*ou em um diretório pai. Quando você cria uma instância de computação, esse arquivo é adicionado ao diretório correto na VM para você.

## <a name="upgrade"></a>Atualizar para o Enterprise Edition

Você pode atualizar seu espaço de trabalho de edição básica para Enterprise Edition para aproveitar os recursos aprimorados, como experiências de código baixo e recursos de segurança aprimorados.

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com).

1. Selecione o espaço de trabalho que você deseja atualizar.

1. Selecione **saiba mais** na parte superior direita da página.

   [![atualizar um espaço de trabalho](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selecione **Atualizar** na janela exibida.


> [!IMPORTANT]
> Não é possível fazer downgrade de um espaço de trabalho Enterprise Edition para um espaço de trabalho básico. 

## <a name="view"></a>Localizar um espaço de trabalho

1. No campo de pesquisa superior, digite **Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Procurar Azure Machine Learning espaço de trabalho](./media/how-to-manage-workspace/find-workspaces.png)

1. Consultar a lista de áreas de trabalho foi encontrado. Pode filtrar com base na subscrição, grupos de recursos e localizações.  

1. Selecione um espaço de trabalho para exibir suas propriedades.

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Utilize o botão de eliminação na parte superior da área de trabalho que pretende eliminar.

  ![Botão de eliminar](./media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

## <a name="next-steps"></a>Passos seguintes

Siga o tutorial de tamanho completo para aprender a usar um espaço de trabalho para criar, treinar e implantar modelos com o Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Modelos de formação](tutorial-train-models-with-aml.md)
