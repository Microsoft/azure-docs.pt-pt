---
title: 'Tutorial: Começa em Cadernos Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Configuração para tutoriais do Jupyter Notebook. Crie um espaço de trabalho, clone cadernos no espaço de trabalho e crie uma instância computacional onde você execute os cadernos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: ed7a95ea83cacc0eaccfa23cd87403fb9be615ee
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210674"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Tutorial: Começa com a Azure Machine Learning em Cadernos Jupyter

Neste tutorial, você completa os passos para começar com Azure Machine Learning usando Cadernos Jupyter em uma [estação de trabalho gerida baseada em nuvem (instância compute)](concept-compute-instance.md). Este tutorial é um precursor de todos os outros tutoriais do Jupyter Notebook.

Neste tutorial:

> [!div class="checklist"]
> * Crie um espaço de trabalho para [aprendizagem automática Azure](concept-workspace.md) para usar em outros tutoriais do Jupyter Notebook.
> * Clone o caderno de tutoriais para a sua pasta no espaço de trabalho.
> * Crie uma instância computacional baseada em nuvem, que lhe dá um ambiente com Azure Machine Learning Python SDK já instalado e configurado para si.

Este tutorial prepara-o para executar cadernos Jupyter num recurso de computação no seu espaço de trabalho.  

Outra ótima maneira de começar com a aprendizagem da Azure Machine é submetendo trabalhos em lote.  Ver [Tutorial: Começar com Azure Machine Learning no seu ambiente de desenvolvimento.](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de machine learning. Liga a sua subscrição e grupo de recursos Azure a um objeto facilmente consumido no serviço.

Salte para [Clonar uma pasta de portátil](#clone) se já tiver um espaço de trabalho de Aprendizagem automática Azure.  

Existem muitas [formas de criar um espaço de trabalho.](how-to-manage-workspace.md)  Neste tutorial, cria-se um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Tome nota do seu espaço de *trabalho* e *subscrição.* Vai precisar desta informação para garantir que cria a sua experiência no local certo.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>Executar um caderno no seu espaço de trabalho

O Azure Machine Learning inclui um servidor de cadernos em nuvem no seu espaço de trabalho para uma experiência sem instalação e pré-configurada. Use [o seu próprio ambiente](tutorial-1st-experiment-sdk-setup-local.md) se preferir ter controlo sobre o seu ambiente, pacotes e dependências.

 Acompanhe este vídeo ou use os passos detalhados para clonar e executar o caderno tutorial a partir do seu espaço de trabalho.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Clone uma pasta de caderno

Você completa a seguinte configuração de experiências e executar passos no estúdio Azure Machine Learning. Esta interface consolidada inclui ferramentas de machine learning para realizar cenários de ciência de dados para os praticantes de ciência de dados de todos os níveis de habilidade.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/).

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. À esquerda, selecione **Cadernos.**

1. No topo, selecione o **separador Amostras.**

1. Abra a pasta **Python.**

1. Abra a pasta com um número de versão. Este número representa a versão atual para o Python SDK.

1. Selecione o **botão ...** à direita da pasta **tutoriais** e, em seguida, selecione **Clone**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Screenshot que mostra a pasta de tutoriais clone.":::

1. Uma lista de pastas mostra cada utilizador que acede ao espaço de trabalho. Selecione a sua pasta para clonar a pasta **de tutoriais**  lá.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Abra o caderno clonado

1. Abra a pasta **de tutoriais** que foi fechada na secção **de ficheiros do utilizador.**

    > [!IMPORTANT]
    > Pode ver cadernos na pasta de **amostras,** mas não pode executar um caderno a partir daí. Para executar um caderno, certifique-se de que abre a versão clonada do caderno na secção **Ficheiros de Utilizador.**
    
1. Selecione o ficheiro **img-classification-part1-training.ipynb** na sua pasta **de tutoriais/classificação de imagem-mnist-data.**

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Screenshot que mostra a pasta de tutoriais Aberto.":::

1. Na barra superior, selecione uma instância de computação para usar para executar o caderno. Estas máquinas virtuais (VMs) estão pré-configuradas com [tudo o que precisa para executar Azure Machine Learning](concept-compute-instance.md#contents).

1. Se não forem encontrados VMs, **selecione + Adicione** para criar o VM de instância de cálculo.

    1. Quando criar um VM, siga estas regras:
 
        + Um nome é necessário, e o campo não pode estar vazio.
        + O nome deve ser único (de forma insensível a casos) em todos os casos de computação existentes na região de Azure do espaço de trabalho ou da instância computacional. Receberá um alerta se o nome que escolher não for único.
        + Os caracteres válidos são letras maiúsculas e minúsculas, números 0 a 9 e o traço (-).
        + O nome deve ter entre 3 e 24 caracteres.
        + O nome deve começar com uma letra, não um número ou um traço.
        + Se for utilizado um traço, deve ser seguido por pelo menos uma letra após o traço. Por exemplo, o teste-, o teste-0, o teste-01 são inválidos, enquanto o teste-a0, o teste-0a são instâncias válidas.

    1. Selecione o tamanho VM das opções disponíveis. Para os tutoriais, o VM padrão é uma boa escolha.

    1. Em seguida, selecione **Criar**. Pode levar aproximadamente cinco minutos para configurar o seu VM.

1. Quando o VM está disponível, aparece na barra de ferramentas superior. Agora pode executar o caderno utilizando **tudo** na barra de ferramentas ou **Shift+Enter** nas células de código do caderno.

Se tiver widgets personalizados ou preferir usar Jupyter ou JupyterLab, selecione a lista de drop-down **jupyter** na extrema-direita. Em seguida, selecione **Jupyter** ou **JupyterLab**. A nova janela do navegador abre.

## <a name="next-steps"></a>Passos seguintes

Agora que tem um ambiente de desenvolvimento montado, continue a treinar um modelo num Caderno Jupyter.

> [!div class="nextstepaction"]
> [Tutorial: Modelos de classificação de imagem de comboio com dados do MNIST e scikit-learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Se não planeia seguir outros tutoriais agora, pare o servidor de cadernos de nuvem VM quando não estiver a usá-lo para reduzir o custo.

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
