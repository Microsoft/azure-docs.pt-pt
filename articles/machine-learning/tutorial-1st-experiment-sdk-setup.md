---
title: 'Tutorial: Crie a sua primeira experiência ML'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você vai começar com o Azure Machine Learning Python SDK em cadernos Jupyter.  Na Parte 1, cria-se um espaço de trabalho no qual irá gerir experiências e modelos ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: a6f977c0cdca670b40ccdc01db64a493962e3dda
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165981"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Tutorial: Começar a criar a sua primeira experiência ML com o Python SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você completa os passos de ponta a ponta para começar com o Azure Machine Learning Python SDK correndo em cadernos Jupyter. Este tutorial é **parte uma de uma série tutorial em duas partes,** e abrange a configuração e configuração do ambiente Python, bem como a criação de um espaço de trabalho para gerir as suas experiências e modelos de machine learning. [**A segunda parte**](tutorial-1st-experiment-sdk-train.md) baseia-se nisto para formar vários modelos de machine learning e introduzir o processo de gestão de modelos utilizando tanto o estúdio Azure Machine Learning como o SDK.

Neste tutorial:

> [!div class="checklist"]
> * Crie um espaço de trabalho de [aprendizagem automática Azure](concept-workspace.md) para usar no próximo tutorial.
> * Clone o notebook tutoriais em sua pasta no espaço de trabalho.
> * Crie uma instância computacional baseada em nuvem com O SDK de Aprendizagem automática Azure instalado e pré-configurado.


Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Liga o seu grupo de subscrição e recursos Azure a um objeto facilmente consumido no serviço. 

Cria um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Tome nota do seu espaço de **trabalho** e **subscrição.** Vai precisar disto para garantir que cria a sua experiência no lugar certo. 

## <a name="azure"></a>Executar caderno no seu espaço de trabalho

Este tutorial utiliza o servidor de portátil em nuvem no seu espaço de trabalho para uma experiência sem instalação e pré-configurada. Use [o seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controlo sobre o seu ambiente, pacotes e dependências.

Acompanhe este vídeo ou use os passos detalhados abaixo para clonar e executar o tutorial a partir do seu espaço de trabalho. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>Clone uma pasta de caderno

Completa as seguintes etapas de experimentação no estúdio Azure Machine Learning, uma interface consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade.

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. Selecione **Cadernos** à esquerda.

1. Abra a pasta **Amostras.**

1. Abra a pasta **Python.**

1. Abra a pasta com um número de versão.  Este número representa a versão atual para o Python SDK.

1. Selecione o **"..."** à direita da pasta dos **tutoriais** e, em seguida, selecione **Clone**.

    ![Pasta clone](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Uma lista de exibições de pastas mostrando cada utilizador que acede ao espaço de trabalho.  Selecione a pasta para clonar a pasta dos **tutoriais.**

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">Abrir o caderno clonado

1. Em **Ficheiros de Utilizador** abra a pasta e abra a pasta dos **tutoriais** clonados.

    ![Pasta de tutoriais abertos](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Pode ver cadernos na pasta **de amostras,** mas não pode executar um caderno a partir daí.  Para executar um caderno, certifique-se de que abre a versão clonada do caderno na secção Ficheiros do **Utilizador.**
    
1. Selecione o ficheiro **tutorial-1st-experiment-sdk-train.ipynb** nos seus **tutoriais/pasta create-first-ml-experiment.**

1. Na barra superior, selecione uma instância de cálculo para utilizar para executar o caderno. Estes VMs estão pré-configurados com tudo o [que precisa para executar o Azure Machine Learning.](concept-compute-instance.md#contents) Pode selecionar um VM criado por qualquer utilizador do seu espaço de trabalho. 

1. Se não forem encontrados VMs, selecione **+ Adicione** para criar a instância computacional VM. 

    1. Quando criar um VM, forneça um nome.  O nome deve estar entre 2 a 16 caracteres. Os caracteres válidos são letras, dígitos e o - personagem, e também devem ser únicos em toda a sua subscrição Azure.

    1.  Selecione o tamanho da Máquina Virtual a partir das escolhas disponíveis.

    1. Em seguida, selecione **Criar**. Pode levar aproximadamente 5 minutos para configurar o seu VM.

1. Uma vez disponível o VM, será apresentado na barra de ferramentas superior.  Agora pode executar o caderno utilizando **executar tudo** na barra de ferramentas, ou utilizando o **Shift+Enter** nas células de código do caderno.

Se tiver widgets personalizados ou preferir usar o Jupyter/JupyterLab, selecione o **Jupyter** descer na extrema direita, então selecione **Jupyter** ou **JupyterLab**. A nova janela do navegador será aberta.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, completou estas tarefas:

* Criou um espaço de trabalho azure machine learning.
* Criou e configurou um servidor de portátil em nuvem no seu espaço de trabalho.

Na **segunda parte** do tutorial, executa o código em `tutorial-1st-experiment-sdk-train.ipynb` para treinar um modelo de aprendizagem automática. 

> [!div class="nextstepaction"]
> [Tutorial: Treine o seu primeiro modelo](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Se não planeia seguir a parte 2 deste tutorial ou qualquer outro tutoriais, deverá [parar o VM do servidor](tutorial-1st-experiment-sdk-train.md#clean-up-resources) de blocos de notas em nuvem quando não o estiver a utilizar para reduzir o custo.


