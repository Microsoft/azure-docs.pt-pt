---
title: Criar espaços de trabalho no portal
titleSuffix: Azure Machine Learning
description: Aprenda a criar, ver e apagar espaços de trabalho de Aprendizagem automática Azure no portal Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c852d416b73ba29b22efe63996835deac3a5277d
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167679"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Criar e gerir espaços de trabalho de aprendizagem automática Azure no portal Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você vai criar, visualizar e eliminar [**espaços de trabalho de Aprendizagem automática Azure**](concept-workspace.md) no portal Azure para [aprendizagem automática Azure](overview-what-is-azure-ml.md).  O portal é a forma mais fácil de começar com espaços de trabalho, mas à medida que as suas necessidades mudam ou os requisitos para o aumento da automatização também pode criar e apagar espaços de trabalho [utilizando o CLI,](reference-azure-machine-learning-cli.md) [com o código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou através da [extensão do Código VS.](tutorial-setup-vscode-extension.md)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Para criar um espaço de trabalho, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando as credenciais para a sua subscrição Azure. 

1. No canto superior esquerdo do portal Azure, selecione **+ Criar um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Utilize a barra de pesquisa para encontrar **machine learning**.

1. Selecione **Machine Learning**.

1. No painel **de aprendizagem** automática, selecione **Criar** para começar.

1. Forneça as seguintes informações para configurar o seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome da área de trabalho |Insira um nome único que identifique o seu espaço de trabalho. Neste exemplo, **usamos docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de recordar e diferenciar dos espaços de trabalho criados por outros. O nome do espaço de trabalho é insensível a caso.
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Utilize um grupo de recursos já existente na sua subscrição ou introduza um nome para criar um grupo de recursos novo. Um grupo de recursos detém recursos relacionados para uma solução Azure. Neste exemplo, **usamos docs-aml**. 
   Localização | Selecione a localização mais próxima dos seus utilizadores e os recursos de dados para criar o seu espaço de trabalho.
   Edição do espaço de trabalho | Selecione **Basic** ou **Enterprise**.  Esta edição do espaço de trabalho determina as funcionalidades a que terá acesso e preços. Saiba mais sobre [as ofertas de edição Básica e Empresarial.](overview-what-is-azure-ml.md#sku) 

    ![Configure o seu espaço de trabalho](./media/how-to-manage-workspace/select-edition.png)

1. Quando terminar de configurar o espaço de trabalho, selecione **Review + Create**.
2. Reveja as definições e escdave quaisquer alterações ou correções adicionais. Quando estiver satisfeito com as definições, **selecione Criar**.

   > [!Warning] 
   > Pode levar vários minutos para criar o seu espaço de trabalho na nuvem.

   Quando o processo está concluído, aparece uma mensagem de sucesso de implantação. 
 
 1. Para visualizar o novo espaço de trabalho, selecione **Ir para o recurso**.

### <a name="download-a-configuration-file"></a>Descarregue um ficheiro de configuração

1. Se estiver a criar uma [instância computacional,](tutorial-1st-experiment-sdk-setup.md#azure)ignore este passo.

1. Se pretender utilizar código no seu ambiente local que faça referência a este espaço de trabalho, selecione **Baixar config.jsna** secção **visão geral** do espaço de trabalho.  

   ![Baixar config.jsem](./media/how-to-manage-workspace/configure.png)
   
   Coloque o ficheiro na estrutura do diretório com os seus scripts Python ou Cadernos Jupyter. Pode estar no mesmo diretório, num subdiretório chamado *.azureml,* ou num diretório-mãe. Quando cria uma instância de computação, este ficheiro é adicionado ao diretório correto no VM para si.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Upgrade para edição enterprise

Você pode atualizar o seu espaço de trabalho da edição Basic para a edição Enterprise para tirar partido das funcionalidades melhoradas, tais como experiências de baixo código e funcionalidades de segurança melhoradas.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. Selecione o espaço de trabalho que deseja atualizar.

1. **Selecione Saiba mais** no topo direito da página.

   [![Atualizar um espaço de ](./media/how-to-manage-workspace/upgrade.png) trabalho](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selecione **Upgrade** na janela que aparece.


> [!IMPORTANT]
> Não é possível desvalorizar um espaço de trabalho da edição enterprise para um espaço de trabalho de edição básica. 

## <a name="find-a-workspace"></a><a name="view"></a>Encontre um espaço de trabalho

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No campo de pesquisa superior, **escreva Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Pesquisa de espaço de trabalho para aprendizagem automática Azure](./media/how-to-manage-workspace/find-workspaces.png)

1. Veja a lista de espaços de trabalho encontrados. Pode filtrar com base em subscrições, grupos de recursos e locais.  

1. Selecione um espaço de trabalho para exibir as suas propriedades.

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

No [portal Azure,](https://portal.azure.com/) **selecione Delete** na parte superior do espaço de trabalho que pretende eliminar.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Apagar espaço de trabalho":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Mover o espaço de trabalho

> [!WARNING]
> Mover o seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou mover a subscrição proprietária para um novo inquilino, não é suportado. Fazê-lo pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Eliminação do Registo do Contentor de Azure

O espaço de trabalho Azure Machine Learning utiliza o Registo do Contentor Azure (ACR) para algumas operações. Criará automaticamente uma instância ACR quando precisa primeiro de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passos seguintes

Siga o tutorial de corpo inteiro para aprender a usar um espaço de trabalho para construir, treinar e implementar modelos com Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Modelos de comboio](tutorial-train-models-with-aml.md)