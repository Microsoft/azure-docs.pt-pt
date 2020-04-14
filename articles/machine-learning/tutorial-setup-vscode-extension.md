---
title: 'Tutorial: Configurar a extensão do Código do Estúdio Visual'
titleSuffix: Azure Machine Learning
description: Saiba como configurar a extensão visual do Código de Machine Learning do Estúdio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: 731ab18346ac9f100862174312c2c9950026f1eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272929"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Configurar a extensão do código do estúdio visual de aprendizagem automática Azure

Aprenda a instalar e executar scripts utilizando a extensão do Código de Estúdio Visual de Aprendizagem automática Azure.

Neste tutorial, irá aprender as seguintes tarefas:

> [!div class="checklist"]
> * Instale a extensão do Código do Estúdio Visual de Aprendizagem automática Azure
> * Inscreva-se na sua conta Azure a partir do Visual Studio Code
> * Utilize a extensão de Aprendizagem automática Azure para executar um script de amostra

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição do Azure. Se não tiver uma, inscreva-se para experimentar a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Se não o tiver, [instale-o.](https://code.visualstudio.com/docs/setup/setup-overview)
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalar a extensão

1. Abra o Visual Studio Code.
1. Selecione o ícone de **Extensões** da Barra de **Atividade** para abrir a vista Extensões.
1. Na vista Extensões, procure "Azure Machine Learning".
1. Selecione **Instalar**.

    > [!div class="mx-imgBorder"]
    > ![Instale extensão de código VS de aprendizagem automática azure](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Em alternativa, pode instalar a extensão Azure Machine Learning através do Visual Studio [Marketplace, baixando diretamente o instalador](https://aka.ms/vscodetoolsforai). 

Os restantes passos deste tutorial foram testados com a **versão 0.6.8** da extensão.

## <a name="sign-in-to-your-azure-account"></a>Inscreva-se na sua Conta Azure

Para fornecer recursos e executar cargas de trabalho no Azure, tem de assinar com as credenciais da sua conta Azure. Para ajudar na gestão da conta, o Azure Machine Learning instala automaticamente a extensão da Conta Azure. Visite o seguinte site para [saber mais sobre a extensão da Conta Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

1. Abra a paleta de comando selecionando **a Paleta de comando > da** barra de menus. 
1. Introduza o comando "Azure: Inscreva-se" na paleta de comando para iniciar o sinal em processo.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Executar um roteiro de treinamento de modelo de aprendizagem automática em Azure

Agora que assinou no Azure com as credenciais da sua conta, use os passos nesta secção para aprender a usar a extensão para treinar um modelo de aprendizagem automática.

1. Descarregue e desaperte as Ferramentas de Código VS para o [repositório](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) de IA em qualquer lugar do seu computador.
1. Abra `mnist-vscode-docs-sample` o diretório em Visual Studio Code.
1. Selecione o ícone **Azure** na Barra de Atividades.
1. Selecione o ícone **Run Experiment** no topo da Vista de Aprendizagem automática Azure.

    > [!div class="mx-imgBorder"]
    > ![Experiência de execução](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Quando a paleta de comando se expandir, siga as instruções.

    1. Selecione a sua subscrição do Azure.
    1. A partir da lista de ambientes, selecione ficheiro de **dependências Conda**.
    1. Pressione **Entrar** para navegar no ficheiro de dependências da Conda. Este ficheiro contém as dependências necessárias para executar o seu script. Neste caso, o ficheiro de `env.yml` dependências `mnist-vscode-docs-sample` é o ficheiro dentro do diretório.
    1. Pressione **Entrar** para navegar no ficheiro script de treino. Este é o ficheiro que contém código para um modelo de aprendizagem automática que categoriza imagens de dígitos manuscritos. Neste caso, o guião para `train.py` treinar o `mnist-vscode-docs-sample` modelo é o ficheiro dentro do diretório.

1. Neste ponto, um ficheiro de configuração semelhante ao abaixo aparece no editor de texto. A configuração contém as informações necessárias para executar o trabalho de formação como o ficheiro que contém o código para treinar o modelo e quaisquer dependências python especificadas no passo anterior.

    ```json
    {
        "workspace": "WS04131142",
        "resourceGroup": "WS04131142-rg1",
        "location": "South Central US",
        "experiment": "WS04131142-exp1",
        "compute": {
            "name": "WS04131142-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS04131142-com1-rc1",
            "environment": {
                "name": "WS04131142-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. Assim que estiver satisfeito com a sua configuração, submeta a sua experiência abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Submit Experiment
    ```

    Isto envia `train.py` o ficheiro e configuração para o seu espaço de trabalho De Aprendizagem automática Azure. O trabalho de formação é então iniciado com um recurso de computação em Azure.

### <a name="track-the-progress-of-the-training-script"></a>Acompanhe o progresso do roteiro de treino

Executar o seu guião pode levar vários minutos. Para acompanhar o seu progresso:

1. Selecione o ícone **Azure** da barra de atividades.
1. Expanda o seu nó de subscrição.
1. Expanda o nó da experiência atual. Isto está localizado `{workspace}/Experiments/{experiment}` dentro do nó onde os valores para o seu espaço de trabalho e experiência são os mesmos que as propriedades definidas no ficheiro de configuração.
1. Todas as corridas para a experiência estão listadas, bem como o seu estatuto. Para obter o estado mais recente, clique no ícone de atualização no topo da Vista de Aprendizagem automática Azure.

    > [!div class="mx-imgBorder"]
    > ![Track Experiment Progress](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Descarregue o modelo treinado

Quando a execução da experiência está completa, a saída é um modelo treinado. Para descarregar as saídas localmente:

1. Clique na execução mais recente e selecione **Download Outputs**.

    > [!div class="mx-imgBorder"]
    > ![Modelo treinado para descarregar](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Selecione um local para onde guardar as saídas.
1. Uma pasta com o nome da sua execução é descarregada localmente. Navegue até à mesma.
1. Os ficheiros modelo `outputs/outputs/model` estão dentro do diretório.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Treine e implemente um modelo de classificação de imagem TensorFlow utilizando a extensão do código do estúdio visual de aprendizagem automática Azure](tutorial-train-deploy-image-classification-model-vscode.md).
