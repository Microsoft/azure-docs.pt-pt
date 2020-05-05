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
ms.openlocfilehash: 535cf95216cca210b5add5ca22cd6e5b1b997541
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779020"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Tutorial: Começar a criar a sua primeira experiência ML com o Python SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você completa os passos de ponta a ponta para começar com o Azure Machine Learning Python SDK correndo em cadernos Jupyter. Este tutorial é **parte uma de uma série tutorial em duas partes,** e abrange a configuração e configuração do ambiente Python, bem como a criação de um espaço de trabalho para gerir as suas experiências e modelos de machine learning. [**A segunda parte**](tutorial-1st-experiment-sdk-train.md) baseia-se nisto para formar vários modelos de machine learning e introduzir o processo de gestão de modelos utilizando tanto o estúdio Azure Machine Learning como o SDK.

Neste tutorial:

> [!div class="checklist"]
> * Crie um espaço de trabalho de [aprendizagem automática Azure](concept-workspace.md) para usar no próximo tutorial.
> * Clone o caderno de tutoriais para a sua pasta no espaço de trabalho.
> * Crie uma instância computacional baseada em nuvem com O SDK de Aprendizagem automática Azure instalado e pré-configurado.


Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de aprendizagem automática. Liga o seu grupo de subscrição e recursos Azure a um objeto facilmente consumido no serviço. 

Cria um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Tome nota do seu espaço de **trabalho** e **subscrição.** Vai precisar disto para garantir que cria a sua experiência no lugar certo. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Executar caderno no seu espaço de trabalho

Este tutorial utiliza o servidor de portátil em nuvem no seu espaço de trabalho para uma experiência sem instalação e pré-configurada. Use [o seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controlo sobre o seu ambiente, pacotes e dependências.

 Use os passos detalhados abaixo para clonar e executar o tutorial a partir do seu espaço de trabalho. 

### <a name="clone-a-notebook-folder"></a>Clone uma pasta de caderno

Completa as seguintes etapas de experimentação no estúdio Azure Machine Learning, uma interface consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade.

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. Selecione **Cadernos** à esquerda.

1. Selecione o separador **Amostras** na parte superior.

1. Abra a pasta **Python.**

1. Abra a pasta com um número de versão.  Este número representa a versão atual para o Python SDK.

1. Selecione o **"..."** à direita da pasta dos **tutoriais** e, em seguida, selecione **Clone**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Pasta de tutoriais clone":::

1. Uma lista de exibições de pastas mostrando cada utilizador que acede ao espaço de trabalho.  Selecione a pasta para clonar a pasta dos **tutoriais.**

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Abra o caderno clonado

1. Abra a pasta dos **tutoriais** que acabou de ser fechada na secção **de ficheiros do Utilizador.**

    > [!IMPORTANT]
    > Pode ver cadernos na pasta **de amostras,** mas não pode executar um caderno a partir daí.  Para executar um caderno, certifique-se de que abre a versão clonada do caderno na secção Ficheiros do **Utilizador.**
    
1. Selecione o ficheiro **tutorial-1st-experiment-sdk-train.ipynb** nos seus **tutoriais/pasta create-first-ml-experiment.**

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Pasta de tutoriais abertos":::


1. Na barra superior, selecione uma instância de cálculo para utilizar para executar o caderno. Estes VMs estão pré-configurados com tudo o [que precisa para executar o Azure Machine Learning.](concept-compute-instance.md#contents) 

1. Se não forem encontrados VMs, selecione **+ Adicione** para criar a instância computacional VM. 

    1. Quando criar um VM, siga estas regras:  
        + O nome é necessário e não pode estar vazio.
        + O nome tem de ser único (num caso insensível) em todos os casos de computação existentes na região azure do espaço de trabalho/instância computacional. Receberá um alerta se o nome que escolher não for único.
        + Os caracteres válidos são letras maiúsculas e minúsculas, números (0 a 9) e caracteres do traço (-).
        + O nome deve ter entre 3 e 24 caracteres de comprimento.
        + O nome deve começar com uma letra (não um número ou um traço).
        + Se o carácter do traço for utilizado, então deve ser seguido por pelo menos uma letra após o traço. Exemplo: Teste-0, teste-01 são inválidos, enquanto o teste-a0, teste-0a são instâncias válidas.

    1.  Selecione o tamanho da Máquina Virtual a partir das escolhas disponíveis.

    1. Em seguida, selecione **Criar**. Pode levar aproximadamente 5 minutos para configurar o seu VM.

1. Uma vez disponível o VM, será apresentado na barra de ferramentas superior.  Agora pode executar o caderno utilizando **executar tudo** na barra de ferramentas, ou utilizando o **Shift+Enter** nas células de código do caderno.

Se tiver widgets personalizados ou preferir usar o Jupyter/JupyterLab, selecione o **Jupyter** descer na extrema direita, então selecione **Jupyter** ou **JupyterLab**. A nova janela do navegador será aberta.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, completou estas tarefas:

* Criou um espaço de trabalho azure machine learning.
* Criou e configurou um servidor de portátil em nuvem no seu espaço de trabalho.

Na **segunda parte** do tutorial, `tutorial-1st-experiment-sdk-train.ipynb` você executa o código para treinar um modelo de aprendizagem automática. 

> [!div class="nextstepaction"]
> [Tutorial: Treine o seu primeiro modelo](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Se não planeia seguir a parte 2 deste tutorial ou qualquer outro tutoriais, deverá [parar o VM do servidor](tutorial-1st-experiment-sdk-train.md#clean-up-resources) de blocos de notas em nuvem quando não o estiver a utilizar para reduzir o custo.


