---
title: 'Tutorial: Configurar a extensão do Código do Estúdio Visual (pré-visualização)'
titleSuffix: Azure Machine Learning
description: Saiba como configurar a extensão de Aprendizagem automática do Código do Estúdio Visual Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
ms.openlocfilehash: 48fb2ce3672634af202500ccb256e7730933fff8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86199763"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension-preview"></a>Configurar a extensão do Código do Estúdio Visual de Aprendizagem de Máquinas Azure (pré-visualização)

Aprenda a instalar e executar scripts utilizando a extensão Azure Machine Learning Visual Studio Code.

Neste tutorial, irá aprender as seguintes tarefas:

> [!div class="checklist"]
> * Instale a extensão do Código do Estúdio Visual de Aprendizagem visual Azure Machine Learning
> * Inscreva-se na sua conta Azure a partir do Código do Estúdio Visual
> * Use a extensão de aprendizagem automática Azure para executar um script de amostra

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição do Azure. Se não tiver um, inscreva-se para experimentar a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Se não o tiver, [instale-o.](https://code.visualstudio.com/docs/setup/setup-overview)
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalar a extensão

1. Abra o Visual Studio Code.
1. Selecione o ícone **extensões** da Barra de **Atividade** para abrir a vista extensões.
1. Na visão Extensões, procure "Azure Machine Learning".
1. Selecione **Instalar**.

    > [!div class="mx-imgBorder"]
    > ![Instalar extensão de código VS machine learning Azure](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Em alternativa, pode instalar a extensão Azure Machine Learning através do Visual Studio [Marketplace, descarregando diretamente o instalador](https://aka.ms/vscodetoolsforai). 

Os restantes passos deste tutorial foram testados com **a versão 0.6.8** da extensão.

## <a name="sign-in-to-your-azure-account"></a>Inscreva-se na sua Conta Azure

Para obter recursos e executar cargas de trabalho no Azure, tem de assinar com as suas credenciais de conta Azure. Para ajudar na gestão de conta, o Azure Machine Learning instala automaticamente a extensão da Conta Azure. Visite o seguinte site para [saber mais sobre a extensão da Conta Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

1. Abra a paleta de comando selecionando **a Paleta de Comando Ver >** da barra de menu. 
1. Introduza o comando "Azure: Iniciar" na paleta de comando para iniciar o sinal em processo.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Executar um roteiro de treinamento de modelo de aprendizagem de máquinas em Azure

Agora que assinou no Azure com as suas credenciais de conta, use os passos nesta secção para aprender a usar a extensão para treinar um modelo de machine learning.

1. Faça o download e desaperte as [Ferramentas de Código VS para repositório de IA](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) em qualquer lugar do seu computador.
1. Abra o `mnist-vscode-docs-sample` diretório no Código do Estúdio Visual.
1. Selecione o ícone **Azure** na Barra de Atividades.
1. Selecione o ícone **Run Experiment** no topo da Vista de Aprendizagem de Máquinas Azure.

    > [!div class="mx-imgBorder"]
    > ![Experiência de execução](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Quando a paleta de comando se expandir, siga as indicações.

    > [!NOTE]
    > Se já dispõe de recursos existentes para a Azure Machine Learning, veja [como executar experiências no guia do Código VS.](./how-to-manage-resources-vscode.md#run-experiment)

    1. Selecione a sua subscrição do Azure.
    1. Na lista de ambientes, selecione **ficheiro de dependências Conda**.
    1. Pressione **Insira** para navegar no ficheiro de dependências conda. Este ficheiro contém as dependências necessárias para executar o seu script. Neste caso, o ficheiro de dependências é o `env.yml` ficheiro dentro do `mnist-vscode-docs-sample` diretório.
    1. Pressione **Insira** para navegar no ficheiro do script de treino. Este é o ficheiro que contém código para um modelo de aprendizagem automática que categoriza imagens de dígitos manuscritos. Neste caso, o script para treinar o modelo é o `train.py` ficheiro dentro do `mnist-vscode-docs-sample` diretório.

1. Neste ponto, um ficheiro de configuração semelhante ao abaixo aparece no editor de texto. A configuração contém as informações necessárias para executar o trabalho de treino como o ficheiro que contém o código para treinar o modelo e quaisquer dependências python especificadas no passo anterior.

    ```json
    {
        "workspace": "WS06271500",
        "resourceGroup": "WS06271500-rg2",
        "location": "South Central US",
        "experiment": "WS06271500-exp1",
        "compute": {
            "name": "WS06271500-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS06271500-com1-rc1",
            "environment": {
                "name": "WS06271500-env1",
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

1. Uma vez satisfeito com a sua configuração, submeta a sua experiência abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Submit Experiment
    ```

    Isto envia o `train.py` ficheiro e configuração para o seu espaço de trabalho Azure Machine Learning. O trabalho de formação é então iniciado com um recurso compute em Azure.

### <a name="track-the-progress-of-the-training-script"></a>Acompanhe o progresso do roteiro de treino

Executar o seu script pode levar vários minutos. Para acompanhar o seu progresso:

1. Selecione o ícone **Azure** da barra de atividade.
1. Expanda o seu nó de subscrição.
1. Expanda o nó da sua experiência atualmente em execução. Isto está localizado dentro do `{workspace}/Experiments/{experiment}` nó onde os valores para o seu espaço de trabalho e experiência são os mesmos que as propriedades definidas no ficheiro de configuração.
1. Todas as corridas para a experiência estão listadas, bem como o seu estatuto. Para obter o estado mais recente, clique no ícone de atualização no topo da Azure Machine Learning View.

    > [!div class="mx-imgBorder"]
    > ![Track Experiment Progress](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Descarregue o modelo treinado

Quando a experiência estiver completa, a saída é um modelo treinado. Para descarregar as saídas localmente:

1. Clique com o botão direito na execução mais recente e selecione **Download Outputs**.

    > [!div class="mx-imgBorder"]
    > ![Baixar Modelo Treinado](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Selecione um local para guardar as saídas.
1. Uma pasta com o nome da sua execução é descarregada localmente. Navegue até à mesma.
1. Os ficheiros dos modelos estão dentro do `outputs/outputs/model` diretório.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Treine e implemente um modelo de classificação de imagem TensorFlow utilizando a extensão do código do estúdio visual Azure Machine Learning.](tutorial-train-deploy-image-classification-model-vscode.md)
