---
title: 'Tutorial: treinar e implantar um modelo TensorFlow de classificação de imagem usando a extensão Visual Studio Code de Azure Machine Learning'
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implantar um modelo de classificação de imagem usando o TensorFlow e a extensão de Visual Studio Code de Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 899681f2bb9c3ef2a0368015a58db30a843738f5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157557"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Treinar e implantar um modelo de TensorFlow de classificação de imagem usando a extensão de Visual Studio Code de Azure Machine Learning

Saiba como treinar um modelo de classificação de imagem para reconhecer números escritos manualmente usando o TensorFlow e a extensão de Visual Studio Code de Azure Machine Learning.

Neste tutorial, irá aprender as seguintes tarefas:

> [!div class="checklist"]
> * Compreender o código
> * Criar áreas de trabalho
> * Criar uma experimentação
> * Configurar destinos do computador
> * Executar um arquivo de configuração
> * Preparar um modelo
> * Registar um modelo
> * Implementar um modelo

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição do Azure. Se você não tiver uma, Inscreva-se para experimentar a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
- Instale o [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), um editor de código leve de plataforma cruzada.
- Extensão de Visual Studio Code de Azure Machine Learning Studio. Para obter instruções de instalação, consulte o [tutorial de extensão de Visual Studio Code de instalação Azure Machine Learning](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Compreender o código

O código para este tutorial usa TensorFlow para treinar um modelo de aprendizado de máquina de classificação de imagem que categoriza os dígitos manuscritos de 0-9. Ele faz isso criando uma rede neural que usa os valores de pixel de 28 PX x 28 PX Image como entrada e gera uma lista de 10 probabilidades, uma para cada um dos dígitos que estão sendo classificados. Veja abaixo um exemplo de como os dados são parecidos.  

![MNIST dígitos](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Obtenha o código deste tutorial baixando e descompactando as [ferramentas de vs Code para o repositório de ia](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) em qualquer lugar no computador.

## <a name="create-a-workspace"></a>Criar áreas de trabalho

A primeira coisa que você precisa fazer para criar um aplicativo no Azure Machine Learning é criar um espaço de trabalho. Um espaço de trabalho contém os recursos para treinar modelos, bem como os próprios modelos treinados. Para obter mais informações, consulte [o que é um espaço de trabalho](./concept-workspace.md). 

1. Na barra de atividade do Visual Studio Code, selecione o ícone **do Azure** para abrir a exibição Azure Machine Learning.
1. Clique com o botão direito do mouse em sua assinatura do Azure e selecione **criar espaço de trabalho**. 
    
    > [!div class="mx-imgBorder"]
    > ![Criar uma área de trabalho](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Por padrão, um nome é gerado contendo a data e hora de criação. Na paleta de comandos, altere o nome para "TeamWorkspace" e pressione **Enter**.
1. Selecione **criar um novo grupo de recursos** na paleta de comandos. 
1. Digite "TeamWorkspace-RG" na caixa de texto paleta de comandos e pressione **Enter**. 
1. Na paleta de comandos, escolha um local para seu espaço de trabalho. É recomendável escolher um local mais próximo do local em que você planeja implantar seu modelo. Nesse caso, escolha **oeste dos EUA 2**.
1. Quando for solicitado a selecionar um SKU de espaço de trabalho, selecione **básico** para criar um espaço de trabalho básico. Para obter mais informações sobre diferentes ofertas de espaço de trabalho, consulte [Azure Machine Learning visão geral](./overview-what-is-azure-ml.md#sku).

Neste ponto, é feita uma solicitação ao Azure para criar um novo espaço de trabalho em sua conta. Após alguns minutos, o novo espaço de trabalho aparecerá em seu nó de assinatura. 

## <a name="create-an-experiment"></a>Criar uma experimentação

Um ou mais experimentos podem ser criados em seu espaço de trabalho para rastrear e analisar execuções de treinamento de modelo individual. As execuções podem ser feitas na nuvem do Azure ou em seu computador local.

1. Na barra de atividade do Visual Studio Code, selecione o ícone **do Azure** . A exibição Azure Machine Learning é exibida.
1. Expanda seu nó de assinatura.
1. Expanda o nó **TeamWorkspace** . 
1. Clique com o botão direito do mouse no nó **experimentos** .
1. Selecione **criar experimento** no menu de contexto.

    > [!div class="mx-imgBorder"]
    > ![Criar uma experimentação](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. No prompt da paleta de comandos, nomeie seu experimento como "MNIST" e pressione **Enter** para criar o novo experimento. 

Assim como os espaços de trabalho, uma solicitação é enviada ao Azure para criar um experimento com as configurações fornecidas. Após alguns minutos, o novo experimento aparecerá no nó *experimentos* do seu espaço de trabalho. 

## <a name="configure-compute-targets"></a>Configurar destinos de computação

Um destino de computação é o ambiente ou recurso de computação em que você executa scripts e implanta modelos treinados. Para obter mais informações, consulte a [documentação destinos de computação Azure Machine Learning](./concept-compute-target.md).

Para criar um destino de computação:

1. Na barra de atividade do Visual Studio Code, selecione o ícone **do Azure** . A exibição Azure Machine Learning é exibida. 
1. Expanda seu nó de assinatura. 
1. Expanda o nó **TeamWorkspace** . 
1. Sob o nó de área de trabalho, clique com botão direito a **computação** nó e escolha **criar computação**. 

    > [!div class="mx-imgBorder"]
    > ![criar um destino de computação](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Selecione **Azure Machine Learning computação (AmlCompute)** . Azure Machine Learning computação é uma infraestrutura de computação gerenciada que permite ao usuário criar facilmente uma computação de um ou vários nós que pode ser usada com outros usuários em seu espaço de trabalho.
1. Selecionar o tamanho da VM. No prompt da paleta de comandos, selecione **Standard_F2s_v2**. O tamanho da sua VM tem um impacto na quantidade de tempo que leva para treinar seus modelos. Para obter mais informações sobre tamanhos de VM, consulte [tamanhos de máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
1. No prompt da paleta de comandos, nomeie a computação "TeamWkspc-com" e pressione **Enter** para criar sua computação.

Após alguns minutos, o novo destino de computação aparecerá no nó de *computação* do seu espaço de trabalho.

## <a name="create-a-run-configuration"></a>Criar uma configuração de execução

Quando você envia uma execução de treinamento para um destino de computação, também envia a configuração necessária para executar o trabalho de treinamento. Por exemplo, o script que contém o código de treinamento e as dependências do Python necessárias para executá-lo.

Para criar uma configuração de execução:

1. Na barra de atividade do Visual Studio Code, selecione o ícone **do Azure** . A exibição Azure Machine Learning é exibida. 
1. Expanda seu nó de assinatura. 
1. Expanda o nó **TeamWorkspace** . 
1. No nó do espaço de trabalho, clique com o botão direito do mouse no nó de computação **TeamWkspc-com** e escolha **criar configuração de execução**.

    > [!div class="mx-imgBorder"]
    > ![criar uma](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png) de configuração de execução

1. No prompt da paleta de comandos, nomeie a configuração de execução "MNIST-RC" e pressione **Enter** para criar sua computação.
1. Em seguida, selecione **treinamento de nó único do TensorFlow** como o tipo de trabalho de treinamento.
1. Pressione **Enter** para procurar o arquivo de script a ser executado na computação. Nesse caso, o script para treinar o modelo é o `train.py` arquivo dentro do diretório `vscode-tools-for-ai/mnist-vscode-docs-sample`.
1. Insira o seguinte no prompt da paleta de comandos para especificar os pacotes necessários.
    
    ```text
    pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
    ```
    
    Um arquivo chamado `MNIST-rc.runconfig` aparece em VS Code com conteúdo semelhante ao mostrado abaixo:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "python": {
                "userManagedDependencies": false,
                "condaDependencies": {
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                }
            },
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:0.2.4",
                "enabled": true,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                }
            }
        },
        "nodeCount": 1,
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Quando estiver satisfeito com a configuração, abra a paleta de comandos selecionando **exibir > paleta de comandos**.
1. Insira o comando a seguir na paleta de comandos para salvar o arquivo de configuração de execução.

    ```text
    Azure ML: Save and Continue
    ```

A configuração de execução de `MNIST-rc` é adicionada sob o nó de computação *TeamWkspc-com* .

## <a name="train-the-model"></a>Formar o modelo

Durante o processo de treinamento, um modelo TensorFlow é criado pelo processamento dos dados de treinamento e padrões de aprendizado inseridos dentro dele para cada um dos respectivos dígitos que estão sendo classificados. 

Para executar um experimento de Azure Machine Learning:

1. Na barra de atividade do Visual Studio Code, selecione o ícone **do Azure** . A exibição Azure Machine Learning é exibida. 
1. Expanda seu nó de assinatura. 
1. Expanda o nó **TeamWorkspace > experimentos** . 
1. Clique com o botão direito do mouse no experimento do **MNIST** .
1. Selecione **execute experimentação**.

    > [!div class="mx-imgBorder"]
    > ![executar um experimento](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. Na paleta de comandos, selecione o destino de computação **TeamWkspc-com** .
1. Em seguida, selecione a configuração de execução **MNIST-RC** .
1. Neste ponto, uma solicitação é enviada ao Azure para executar seu experimento no destino de computação selecionado em seu espaço de trabalho. Este processo demora vários minutos. A quantidade de tempo para executar o trabalho de treinamento é afetada por vários fatores, como o tipo de computação e o tamanho dos dados de treinamento. Para acompanhar o progresso de seu experimento, clique com o botão direito do mouse no nó de execução atual e selecione **Exibir executar em portal do Azure**.
1. Quando a caixa de diálogo solicitando abrir um site externo for exibida, selecione **abrir**.

    > [!div class="mx-imgBorder"]
    > ![acompanhar o andamento do experimento](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Quando o modelo for concluído, o rótulo de status ao lado do nó de execução atualizará para "concluído".

## <a name="register-the-model"></a>Registe o modelo

Agora que você treinou seu modelo, você pode registrá-lo em seu espaço de trabalho. 

Para registrar seu modelo:

1. Na barra de atividade do Visual Studio Code, selecione o ícone **do Azure** . A exibição Azure Machine Learning é exibida.
1. Expanda seu nó de assinatura. 
1. Expanda o **TeamWorkspace > experimentos > nó MNIST** .
1. Obtenha as saídas do modelo geradas por meio do treinamento do modelo. Clique com o botão direito do mouse no nó executar **1** execução e selecione **baixar saídas**. 

    > [!div class="mx-imgBorder"]
    > ![baixar as saídas do modelo](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Escolha o diretório para salvar as saídas baixadas. Por padrão, as saídas são colocadas no diretório aberto atualmente no Visual Studio Code.
1. Clique com o botão direito do mouse no nó **modelos** e escolha **registrar modelo**.

    > [!div class="mx-imgBorder"]
    > ![registrar um modelo](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Na paleta de comandos, nomeie o modelo "MNIST-TensorFlow-Model" e pressione **Enter**.
1. Um modelo TensorFlow é composto por vários arquivos. Selecione **pasta de modelo** como o formato de caminho do modelo na paleta de comandos. 
1. Selecione o diretório `azureml_outputs/Run_1/outputs/Run_1/outputs/outputs/model`.

    Um arquivo que contém as configurações do modelo aparece em Visual Studio Code com conteúdo semelhante ao seguinte:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Quando estiver satisfeito com sua configuração, salve-a abrindo a paleta de comandos e inserindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

Depois de alguns minutos, o modelo aparece sob o nó *modelos* .

## <a name="deploy-the-model"></a>Implementar o modelo

No Visual Studio Code, você pode implantar seu modelo como um serviço Web para:

+ ACI (instâncias de contêiner do Azure).
+ AKS (serviço kubernetes do Azure).

Você não precisa criar um contêiner ACI para testar com antecedência, pois os contêineres ACI são criados conforme necessário. No entanto, você precisa configurar clusters AKS com antecedência. Para obter mais informações sobre opções de implantação, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md) .

Para implantar um serviço Web como um ACI:

1. Na barra de atividade do Visual Studio Code, selecione o ícone **do Azure** . A exibição Azure Machine Learning é exibida.
1. Expanda seu nó de assinatura. 
1. Expanda o nó **modelos de > de TeamWorkspace** . 
1. Clique com o botão direito do mouse no **MNIST-TensorFlow-Model** e selecione **implantar serviço do modelo registrado**.

    > [!div class="mx-imgBorder"]
    > ![Implementar o modelo](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Na paleta de comandos, selecione **instâncias de contêiner do Azure**.
1. Nomeie o serviço "mnist-tensorflow-svc" e pressione **Enter** na paleta de comandos.
1. Escolha o script a ser executado no contêiner pressionando **Enter** na paleta de comandos e navegando pelo arquivo de `score.py` no diretório `mnist-vscode-docs-sample`.
1. Forneça as dependências necessárias para executar o script pressionando **Enter** na paleta de comandos e navegando pelo arquivo de `env.yml` no diretório `mnist-vscode-docs-sample`.

    Um arquivo que contém as configurações do modelo aparece em Visual Studio Code com conteúdo semelhante ao seguinte:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```
1. Quando estiver satisfeito com sua configuração, salve-a abrindo a paleta de comandos e inserindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

Neste ponto, uma solicitação é enviada ao Azure para implantar o serviço Web. Este processo demora vários minutos. Depois de implantado, o novo serviço aparecerá sob o nó *pontos de extremidade* .

## <a name="next-steps"></a>Passos seguintes

* Para obter uma explicação de como treinar com Azure Machine Learning fora do Visual Studio Code, consulte [tutorial: treinar modelos com Azure Machine Learning](tutorial-train-models-with-aml.md).
* Para obter uma explicação de como editar, executar e depurar código localmente, consulte o [tutorial do Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).

