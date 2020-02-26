---
title: Criar espaços de trabalho azure machine learning no portal
titleSuffix: Azure Machine Learning
description: Aprenda a criar, visualizar e eliminar espaços de trabalho de Aprendizagem automática Azure no portal Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 4f31c5583671ed3441e1a3f0f53c8518e550080a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77580734"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Criar e gerir espaços de trabalho azure machine learning no portal Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você vai criar, visualizar e eliminar espaços de [**trabalho Azure Machine Learning**](concept-workspace.md) no portal Azure para [Aprendizagem automática Azure](overview-what-is-azure-ml.md).  O portal é a forma mais fácil de começar com espaços de trabalho, mas à medida que as suas necessidades mudam ou os requisitos para a automação aumentam, também pode criar e eliminar espaços de trabalho [utilizando o CLI,](reference-azure-machine-learning-cli.md) [com código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou através da [extensão do Código VS](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Para criar uma área de trabalho, precisa de uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando as credenciais para a sua subscrição Azure. 

1. No canto superior esquerdo do portal Azure, selecione **+ Crie um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Utilize a barra de pesquisa para encontrar **machine learning**.

1. Selecione **Machine Learning**.

1. No painel **machine learning,** selecione **Criar** para começar.

1. Forneça as seguintes informações para configurar o seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome do espaço de trabalho |Introduza um nome único que identifique o seu espaço de trabalho. Neste exemplo, utilizamos **docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de recordar e para diferenciar dos espaços de trabalho criados por outros. O nome do espaço de trabalho é insensível a casos.
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Utilize um grupo de recursos existente na sua subscrição ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos detém recursos relacionados para uma solução Azure. Neste exemplo, utilizamos **docs-aml**. 
   Localização | Selecione a localização mais próxima dos seus utilizadores e os recursos de dados para criar o seu espaço de trabalho.
   Edição do espaço de trabalho | Selecione **Basic** ou **Enterprise**.  Esta edição do espaço de trabalho determina as funcionalidades a que terá acesso e preços. Saiba mais sobre as ofertas de [edição Basic e Enterprise.](overview-what-is-azure-ml.md#sku) 

    ![Configure o seu espaço de trabalho](./media/how-to-manage-workspace/select-edition.png)

1. Depois de terminar de configurar o espaço de trabalho, selecione **Criar**. 

   > [!Warning] 
   > Pode levar vários minutos para criar o seu espaço de trabalho na nuvem.

   Quando o processo está concluído, aparece uma mensagem de sucesso de implantação. 
 
 1. Para ver o novo espaço de trabalho, selecione **Ir para o recurso**.

### <a name="download-a-configuration-file"></a>Descarregue um ficheiro de configuração

1. Se estiver a criar uma [instância de cálculo,](tutorial-1st-experiment-sdk-setup.md#azure)ignore este passo.

1. Se planeia utilizar o código no seu ambiente local que faz referência a este espaço de trabalho, selecione **Download config.json** da secção **de visão geral** do espaço de trabalho.  

   ![Baixar config.json](./media/how-to-manage-workspace/configure.png)
   
   Coloque o ficheiro na estrutura do diretório com os seus scripts Python ou Cadernos Jupyter. Pode estar no mesmo diretório, um subdiretório chamado *.azureml,* ou num directório-mãe. Quando cria uma instância de cálculo, este ficheiro é adicionado ao diretório correto no VM para si.

## <a name="upgrade"></a>Upgrade para edição enterprise

Pode atualizar o seu espaço de trabalho da edição Basic para a edição Enterprise para aproveitar as funcionalidades melhoradas, tais como experiências de baixo código e funcionalidades de segurança melhoradas.

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com)

1. Selecione o espaço de trabalho que pretende atualizar.

1. Selecione **Saiba mais** na parte superior direita da página.

   [![Atualizar um espaço de trabalho](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selecione **Upgrade** na janela que aparece.


> [!IMPORTANT]
> Não é possível desvalorizar um espaço de trabalho da edição da Enterprise para um espaço de trabalho de edição básica. 

## <a name="view"></a>Encontre um espaço de trabalho

1. No campo de pesquisa superior, digite **Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Pesquisa por espaço de trabalho azure machine learning](./media/how-to-manage-workspace/find-workspaces.png)

1. Consultar a lista de áreas de trabalho foi encontrado. Pode filtrar com base na subscrição, grupos de recursos e localizações.  

1. Selecione um espaço de trabalho para exibir as suas propriedades.

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Utilize o botão de eliminação na parte superior da área de trabalho que pretende eliminar.

  ![Botão de eliminar](./media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Movendo o espaço de trabalho

> [!WARNING]
> A mudança do seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou a mudança da subscrição própria para um novo inquilino, não é suportada. Fazê-lo pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Apagando o Registo de Contentores Azure

O espaço de trabalho Azure Machine Learning utiliza o Registo de Contentores Azure (ACR) para algumas operações. Criará automaticamente uma instância ACR quando precisar primeiro de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passos seguintes

Siga o tutorial de corpo inteiro para aprender a usar um espaço de trabalho para construir, treinar e implementar modelos com O Machine Learning Azure.

> [!div class="nextstepaction"]
> [Tutorial: Modelos de comboio](tutorial-train-models-with-aml.md)
