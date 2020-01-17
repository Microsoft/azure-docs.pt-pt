---
title: 'Tutorial: configurar a extensão de Visual Studio Code de Azure Machine Learning'
titleSuffix: Azure Machine Learning
description: Saiba como configurar a extensão de Azure Machine Learning de Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157613"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Configurar a extensão de Visual Studio Code de Azure Machine Learning

Saiba como instalar e executar scripts usando a extensão de Visual Studio Code de Azure Machine Learning.

Neste tutorial, irá aprender as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a extensão de Visual Studio Code de Azure Machine Learning
> * Entre em sua conta do Azure de Visual Studio Code
> * Usar a extensão Azure Machine Learning para executar um script de exemplo

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição do Azure. Se você não tiver uma, Inscreva-se para experimentar a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
- Instale o [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), um editor de código leve de plataforma cruzada. 

## <a name="install-the-extension"></a>Instalar a extensão

1. Abra o Visual Studio Code.
1. Selecione o ícone de **extensões** na **barra de atividades** para abrir o modo de exibição de extensões.
1. No modo de exibição extensões, pesquise "Azure Machine Learning".
1. Selecione **Instalar**.

> [!NOTE]
> Como alternativa, você pode instalar a extensão Azure Machine Learning por meio do Visual Studio Marketplace [baixando o instalador diretamente](https://aka.ms/vscodetoolsforai). 

O restante das etapas deste tutorial foi testado com a **versão 0.6.8** da extensão.

## <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

Para provisionar recursos e executar cargas de trabalho no Azure, você precisa entrar com suas credenciais de conta do Azure. Para auxiliar no gerenciamento de conta, Azure Machine Learning instala automaticamente a extensão de conta do Azure. Visite o site a seguir para [saber mais sobre a extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Abra a paleta de comandos selecionando **exibir > paleta de comandos** na barra de menus. 
1. Insira o comando "Azure: entrar" na caixa de texto para iniciar o processo de entrada.

## <a name="run-a-script-in-azure"></a>Executar um script no Azure

Agora que você entrou no Azure com suas credenciais de conta, use as etapas nesta seção para aprender a usar a extensão para treinar um modelo de aprendizado de máquina.

1. Baixe e descompacte as [ferramentas de vs Code para o repositório de ia](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) em qualquer lugar no computador.
1. Abra o diretório `mnist-vscode-docs-sample` no Visual Studio Code.
1. Selecione o ícone **do Azure** na barra de atividade.
1. Selecione o ícone **executar experimento** na parte superior da exibição de Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![executar experimento](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Quando a paleta de comandos se expandir, siga os prompts.

    1. Selecione a sua subscrição do Azure.
    1. Selecione **criar um novo espaço de trabalho do Azure ml**
    1. Selecione o tipo de trabalho de **treinamento TensorFlow de nó único** .
    1. Insira `train.py` como o script a ser treinado. Esse é o arquivo que contém o código para um modelo de aprendizado de máquina que categoriza imagens de dígitos manuscritos.
    1. Especifique os pacotes a seguir como requisitos para execução.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. Neste ponto, um arquivo de configuração semelhante ao mostrado abaixo é exibido no editor de texto. A configuração contém as informações necessárias para executar o trabalho de treinamento, como o arquivo que contém o código para treinar o modelo e quaisquer dependências do Python especificadas na etapa anterior.

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Selecione **Enviar experimento** para executar seu experimento no Azure. Isso envia a `train.py` e o arquivo de configuração para seu espaço de trabalho do Azure Machine Learning. O trabalho de treinamento é então iniciado em um recurso de computação no Azure.
1. Após alguns minutos, um diretório chamado `output` é criado localmente contendo um modelo TensorFlow treinado.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: treinar e implantar um modelo TensorFlow de classificação de imagem usando a extensão Visual Studio Code de Azure Machine Learning](tutorial-train-deploy-image-classification-model-vscode.md).
