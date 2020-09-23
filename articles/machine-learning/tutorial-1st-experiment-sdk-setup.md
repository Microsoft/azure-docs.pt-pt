---
title: 'Tutorial: Começa em Cadernos Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Configuração para tutoriais do Jupyter Notebook.  Crie um espaço de trabalho para aprendizagem automática Azure, clone os Cadernos Jupyter no espaço de trabalho e crie uma instância computacional onde você execute os cadernos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896894"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Tutorial: Começa com a Azure Machine Learning em Cadernos Jupyter

Neste tutorial, você completa os passos para começar com o Azure Machine Learning usando Cadernos Jupyter em uma [estação de trabalho gerida baseada em nuvem (instância compute)](concept-compute-instance.md). Este tutorial é um precursor de todos os outros tutoriais do Jupyter Notebook.

Neste tutorial:

> [!div class="checklist"]
> * Crie um espaço de trabalho de [aprendizagem de máquinas Azure](concept-workspace.md) para usar em outros tutoriais do Jupyter Notebook.
> * Clone o caderno de tutoriais para a sua pasta no espaço de trabalho.
> * Crie um caso de computação baseado em nuvem com Azure Machine Learning Python SDK instalado e pré-configurado.

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de machine learning. Liga a sua subscrição e grupo de recursos Azure a um objeto facilmente consumido no serviço.

Cria um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Tome nota do seu espaço de **trabalho** e **subscrição.** Vai precisar disto para garantir que cria a sua experiência no lugar certo. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Executar caderno no seu espaço de trabalho

O Azure Machine Learning inclui um servidor de cadernos em nuvem no seu espaço de trabalho para uma experiência sem instalação e pré-configurada. Use [o seu próprio ambiente](tutorial-1st-experiment-sdk-setup-local.md) se preferir ter controlo sobre o seu ambiente, pacotes e dependências.

 Acompanhe este vídeo ou use os passos detalhados abaixo para clonar e executar o caderno tutorial a partir do seu espaço de trabalho.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Clone uma pasta de caderno

Você completa os seguintes passos de configuração de experiências no estúdio Azure Machine Learning, uma interface consolidada que inclui ferramentas de machine learning para executar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/).

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. Selecione **Cadernos** à esquerda.

1. Selecione o separador **Amostras** na parte superior.

1. Abra a pasta **Python.**

1. Abra a pasta com um número de versão.  Este número representa a versão atual para o Python SDK.

1. Selecione o **"..."** à direita da pasta **dos tutoriais** e, em seguida, selecione **Clone**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Pasta de tutoriais de clones":::

1. Uma lista de pastas mostra cada utilizador que acede ao espaço de trabalho.  Selecione a sua pasta para clonar a pasta **de tutoriais**  lá.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Abra o caderno clonado

1. Abra a pasta **de tutoriais** que acabou de ser fechada na secção **de ficheiros do utilizador.**

    > [!IMPORTANT]
    > Pode ver cadernos na pasta das **amostras,** mas não pode executar um caderno a partir daí.  Para executar um caderno, certifique-se de que abre a versão clonada do caderno na secção **Ficheiros de Utilizador.**
    
1. Selecione o arquivo **tutorial-1-experiment-sdk-train.ipynb** na sua pasta **de tutoriais/classificação de imagem-mnist-data.**

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Abrir pasta de tutoriais":::

1. Na barra superior, selecione uma instância de computação para usar para executar o caderno. Estes VMs estão pré-configurados com [tudo o que precisa para executar Azure Machine Learning](concept-compute-instance.md#contents).

1. Se não forem encontrados VMs, **selecione + Adicione** para criar o VM de instância de cálculo. 

    1. Quando criar um VM, siga estas regras:  
        + O nome é necessário e não pode estar vazio.
        + O nome tem de ser único (num caso insensível) em todos os casos de computação existentes na região de Azure do espaço de trabalho/computação. Receberá um alerta se o nome que escolher não for único.
        + Os caracteres válidos são letras maiúsculas e minúsculas, números (0 a 9) e caracteres traço (-).
        + O nome deve ter entre 3 e 24 caracteres.
        + O nome deve começar com uma letra (não um número ou um traço).
        + Se o carácter do traço for utilizado, deve ser seguido por pelo menos uma letra após o traço. Exemplo: O teste-, o teste-0, o teste-01 são inválidos, enquanto o teste-a0, o teste-0a são instâncias válidas.

    1.  Selecione o tamanho da Máquina Virtual nas opções disponíveis. Para os tutoriais, o VM padrão é uma boa escolha.

    1. Em seguida, selecione **Criar**. Pode levar aproximadamente 5 minutos para configurar o seu VM.

1. Uma vez disponível o VM, será exibido na barra de ferramentas superior.  Agora pode executar o caderno utilizando **tudo** na barra de ferramentas ou utilizando o **Shift+Enter** nas células de código do caderno.

Se tiver widgets personalizados ou preferir usar o Jupyter/JupyterLab selecione o **Jupyter** drop down na extrema-direita, então selecione **Jupyter** ou **JupyterLab**. A nova janela do navegador será aberta.

## <a name="next-steps"></a>Passos seguintes

Agora que tem um ambiente de desenvolvimento montado, continue a treinar um modelo num Caderno Jupyter:

> [!div class="nextstepaction"]
> [Tutorial: Modelos de classificação de imagem de comboio com dados do MNIST e scikit-learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Se não planeia seguir outros tutoriais agora, deve parar o servidor de cadernos de nuvem VM quando não estiver a usá-lo para reduzir o custo:

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
