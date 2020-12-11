---
title: 'Tutorial: Modelos de implantação de & de comboio: Código VS (pré-visualização)'
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implementar um modelo de classificação de imagem utilizando o TensorFlow e a extensão do código do estúdio visual de aprendizagem da máquina de azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
ms.custom: contperf-fy20q4
ms.openlocfilehash: 9ad96bdb632e134cf3e3a0f82bb97f88c87e72e9
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033447"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension-preview"></a>Treine e implemente um modelo de classificação de imagem TensorFlow utilizando a extensão do código do estúdio visual de aprendizagem visual Azure Machine Learning (pré-visualização)

Aprenda a treinar e implementar um modelo de classificação de imagem para reconhecer números escritos à mão usando o TensorFlow e a extensão do código do estúdio de aprendizagem visual Azure Machine Learning.

Neste tutorial, irá aprender as seguintes tarefas:

> [!div class="checklist"]
> * Compreender o código
> * Criar uma área de trabalho
> * Criar uma experimentação
> * Configurar alvos de computador
> * Executar um ficheiro de configuração
> * Preparar um modelo
> * Registar um modelo
> * Implementar um modelo

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição do Azure. Se não tiver um, inscreva-se para experimentar a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
- Instale [o Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), um editor de códigos leve e transversal.
- Extensão do Código do Estúdio Visual de Aprendizagem de Máquinas Azure. Para obter instruções de instalação consulte o tutorial de [extensão do Código do Estúdio Visual de Aprendizagem visual de Machine Learning Azure Machine Learning](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Compreender o código

O código para este tutorial usa o TensorFlow para treinar um modelo de aprendizagem de máquinas de classificação de imagem que categoriza os dígitos manuscritos de 0-9. Fá-lo criando uma rede neural que leva os valores de pixel de imagem de 28 px x 28 px como entrada e outputs uma lista de 10 probabilidades, sendo uma para cada um dos dígitos classificados. Abaixo está uma amostra de como os dados parecem.  

![Dígitos MNIST](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Obtenha o código para este tutorial descarregando e desapertando as [Ferramentas de Código VS para repositório de IA](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) em qualquer lugar do seu computador.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

A primeira coisa que tem de fazer para construir uma aplicação no Azure Machine Learning é criar um espaço de trabalho. Um espaço de trabalho contém os recursos para formar modelos, bem como os próprios modelos treinados. Para mais informações, veja [o que é um espaço de trabalho.](./concept-workspace.md) 

1. Na barra de atividades do Código do Estúdio Visual, selecione o ícone **Azure** para abrir a vista Azure Machine Learning.
1. Clique com o botão direito na subscrição do Azure e selecione **Criar espaço de trabalho.** 
    
    > [!div class="mx-imgBorder"]
    > ![Criar uma área de trabalho](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Por predefinição, é gerado um nome contendo a data e a hora da criação. Na caixa de entrada de texto, altere o nome para "TeamWorkspace" e prima **Enter**.
1. Selecione **Criar um novo grupo de recursos.** 
1. Nomeie o seu grupo de recursos "TeamWorkspace-rg" e prima **Enter**. 
1. Escolha um local para o seu espaço de trabalho. Recomenda-se escolher um local mais próximo do local que pretende implementar o seu modelo. Por exemplo, "West US 2".
1. Quando solicitado para selecionar o tipo de espaço de trabalho, escolha **o básico**.

Neste momento, é feito um pedido ao Azure para criar um novo espaço de trabalho na sua conta. Após alguns minutos, o novo espaço de trabalho aparece no seu nó de subscrição. 

## <a name="create-an-experiment"></a>Criar uma experimentação

Uma ou mais experiências podem ser criadas no seu espaço de trabalho para rastrear e analisar as corridas individuais de treino de modelos. As corridas podem ser feitas na nuvem Azure ou na sua máquina local.

1. Na barra de atividades do Código do Estúdio Visual, selecione o ícone **Azure.** Aparece a vista Azure Machine Learning.
1. Expanda o seu nó de subscrição.
1. Expandir o nó **TeamWorkspace.** 
1. Clique com o botão direito no nó **experimentos.**
1. Selecione **Create Experiment** a partir do menu de contexto.

    > [!div class="mx-imgBorder"]
    > ![Criar uma experimentação](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Nomeie a sua experiência "MNIST" e **pressione Enter** para criar a nova experiência. 

Tal como os espaços de trabalho, é enviado um pedido ao Azure para criar uma experiência com as configurações fornecidas. Após alguns minutos, a nova experiência aparece no nó *experimentos* do seu espaço de trabalho. 

## <a name="configure-compute-targets"></a>Configurar metas de computação

Um alvo de cálculo é o recurso de computação ou ambiente onde você executa scripts e implementa modelos treinados. Para obter mais informações, consulte a [documentação de metas de aprendizagem automática Azure.](./concept-compute-target.md)

Para criar um alvo de computação:

1. Na barra de atividades do Código do Estúdio Visual, selecione o ícone **Azure.** Aparece a vista Azure Machine Learning. 
1. Expanda o seu nó de subscrição. 
1. Expandir o nó **TeamWorkspace.** 
1. Sob o nó do espaço de trabalho, clique com o nó de **clusters compute** e escolha **Create Compute**. 

    > [!div class="mx-imgBorder"]
    > ![Criar um alvo de computação](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Selecione **Azure Machine Learning Compute (AmlCompute)**. O Azure Machine Learning Compute é uma infraestrutura de computação gerida que permite ao utilizador criar facilmente um único ou multi-nó compute que pode ser usado com outros utilizadores no seu espaço de trabalho.
1. Selecionar o tamanho da VM. Selecione **Standard_F2s_v2** da lista de opções. O tamanho do seu VM tem um impacto no tempo que leva para treinar os seus modelos. Para obter mais informações sobre tamanhos VM, consulte [os tamanhos das máquinas virtuais Linux em Azure.](../virtual-machines/sizes.md)
1. Nomeie o seu cálculo "TeamWkspc-com" e prima **Enter** para criar o seu cálculo.

    Um ficheiro aparece no Código VS com conteúdo semelhante ao seguinte:

    ```json
    {
        "location": "westus2",
        "tags": {},
        "properties": {
            "computeType": "AmlCompute",
            "description": "",
            "properties": {
                "vmSize": "Standard_F2s_v2",
                "vmPriority": "dedicated",
                "scaleSettings": {
                    "maxNodeCount": 4,
                    "minNodeCount": 0,
                    "nodeIdleTimeBeforeScaleDown": "PT120S"
                }
            }
        }
    }
    ```

1. Quando estiver satisfeito com a configuração, abra a paleta de comando selecionando **a Paleta de Comando Ver >**.
1. Introduza o seguinte comando na paleta de comando para guardar o ficheiro de configuração de execução.

    ```text
    Azure ML: Save and Continue
    ```

Após alguns minutos, o novo alvo de computação aparece no nó de *clusters Compute* do seu espaço de trabalho.

## <a name="create-a-run-configuration"></a>Criar uma configuração de execução

Quando submete uma corrida de treino a um alvo de computação, também submete a configuração necessária para executar o trabalho de formação. Por exemplo, o script que contém o código de treino e as dependências python necessárias para executá-lo.

Para criar uma configuração de execução:

1. Na barra de atividades do Código do Estúdio Visual, selecione o ícone **Azure.** Aparece a vista Azure Machine Learning. 
1. Expanda o seu nó de subscrição. 
1. Expandir o nó **de clusters teamWorkspace > Compute.** 
1. Sob o nó computacional, clique com o botão direito no nó de computação **TeamWkspc-com** e escolha **Configuração De Execução** Create .

    > [!div class="mx-imgBorder"]
    > ![Criar uma configuração de execução](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Nomeie a sua configuração de execução "MNIST-rc" e prima **Enter** para criar a sua configuração de execução.
1. Em seguida, **selecione Criar o novo Azure ML Environment**. Os ambientes definem as dependências necessárias para executar os seus scripts.
1. Nomeie o seu ambiente "MNIST-env" e prima **Enter**.
1. Selecione **o ficheiro de dependências conda** da lista.
1. Pressione **Insira** para navegar no ficheiro de dependências conda. Neste caso, o ficheiro de dependências é o `env.yml` ficheiro dentro do `vscode-tools-for-ai/mnist-vscode-docs-sample` diretório.

    Um ficheiro aparece no Código VS com conteúdo semelhante ao seguinte:

    ```json
    {
        "name": "MNIST-env",
        "version": "1",
        "python": {
            "interpreterPath": "python",
            "userManagedDependencies": false,
            "condaDependencies": {
                "name": "vs-code-azure-ml-tutorial",
                "channels": [
                    "defaults"
                ],
                "dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip",
                    {
                        "pip": [
                            "azureml-defaults"
                        ]
                    }
                ]
            },
            "baseCondaEnvironment": null
        },
        "environmentVariables": {},
        "docker": {
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "baseDockerfile": null,
            "baseImageRegistry": {
                "address": null,
                "username": null,
                "password": null
            },
            "enabled": false,
            "arguments": []
        },
        "spark": {
            "repositories": [],
            "packages": [],
            "precachePackages": true
        },
        "inferencingStackVersion": null
    }
    ```

1. Uma vez satisfeito com a sua configuração, guarde-a abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

1. Esta amostra não utiliza um conjunto de dados registado no Azure Machine Learning. Em vez disso, está carregada quando *train.py* corre. Quando solicitado para criar uma referência de dados para o seu treino, insira "n" no pedido e prima **Enter**.
1. Prima **Insira** para navegar no ficheiro de script para executar no cálculo. Neste caso, o script para treinar o modelo é o `train.py` ficheiro dentro do `vscode-tools-for-ai/mnist-vscode-docs-sample` diretório.

    Um ficheiro chamado `MNIST-rc.runconfig` aparece no Código VS com conteúdo semelhante ao seguinte:

    ```json
    {
        "script": "train.py",
        "arguments": [],
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "name": "MNIST-env",
            "version": "1",
            "python": {
                "interpreterPath": "python",
                "userManagedDependencies": false,
                "condaDependencies": {
                    "name": "vs-code-azure-ml-tutorial",
                    "channels": [
                        "defaults"
                    ],
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        "pip",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                },
                "baseCondaEnvironment": null
            },
            "environmentVariables": {},
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
                "baseDockerfile": null,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                },
                "enabled": false,
                "arguments": []
            },
            "spark": {
                "repositories": [],
                "packages": [],
                "precachePackages": true
            },
            "inferencingStackVersion": null
        },
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Uma vez satisfeito com a sua configuração, guarde-a abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

A `MNIST-rc` configuração de execução é adicionada sob o nó de computação *TeamWkspc-com* e a configuração do `MNIST-env` ambiente é adicionada sob o nó *Ambientes.*

## <a name="train-the-model"></a>Preparar o modelo

Durante o processo de treino, um modelo TensorFlow é criado através do processamento dos dados de formação e dos padrões de aprendizagem incorporados no mesmo para cada um dos respetivos dígitos ser classificado. 

Para executar uma experiência de Aprendizagem automática Azure:

1. Na barra de atividades do Código do Estúdio Visual, selecione o ícone **Azure.** Aparece a vista Azure Machine Learning. 
1. Expanda o seu nó de subscrição. 
1. Expandir o nó **teamWorkspace > Experiments.** 
1. Clique à direita na experiência do **MNIST.**
1. Selecione **Run Experiment**.

    > [!div class="mx-imgBorder"]
    > ![Fazer uma experiência](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. A partir da lista de opções de destino do cálculo, selecione o alvo de computação **TeamWkspc-com.**
1. Em seguida, selecione a configuração de execução **MNIST-rc.**
1. Neste momento, é enviado um pedido ao Azure para executar a sua experiência no alvo de computação selecionado no seu espaço de trabalho. Este processo demora vários minutos. A quantidade de tempo para executar o trabalho de formação é impactada por vários fatores como o tipo de cálculo e o tamanho dos dados de formação. Para acompanhar o progresso da sua experiência, clique com o nó de execução atual e selecione **Ver Run no portal Azure**.
1. Quando o diálogo solicitando a abertura de um website externo aparecer, selecione **Open**.

    > [!div class="mx-imgBorder"]
    > ![Acompanhar o progresso da experiência](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Quando o modelo é feito de formação, a etiqueta de estado ao lado do nó de execução atualiza para "Concluído".

## <a name="register-the-model"></a>Registar o modelo

Agora que treinou o seu modelo, pode registá-lo no seu espaço de trabalho. 

Para registar o seu modelo:

1. Na barra de atividades do Código do Estúdio Visual, selecione o ícone **Azure.** Aparece a vista Azure Machine Learning.
1. Expanda o seu nó de subscrição. 
1. Expandir o **nó teamWorkspace > Experiments > MNIST.**
1. Obtenha as saídas do modelo geradas a partir do treino do modelo. Clique com o botão direito no nó run **1** e selecione **Descarregar saídas**. 

    > [!div class="mx-imgBorder"]
    > ![Baixar saídas de modelos](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Escolha o diretório para guardar as saídas descarregadas para. Por predefinição, as saídas são colocadas no diretório atualmente aberto no Código do Estúdio Visual.
1. Clique com o botão direito no nó **Modelos** e escolha **Registar Modelo.**

    > [!div class="mx-imgBorder"]
    > ![Registar um modelo](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Nomeie o seu modelo "Modelo MNIST-TensorFlow" e prima **Enter**.
1. Um modelo TensorFlow é composto por vários ficheiros. Selecione **a pasta Modelo** como o formato do caminho do modelo a partir da lista de opções. 
1. Selecione o `azureml_outputs/Run_1/outputs/outputs/model` diretório.

    Um ficheiro que contenha as configurações do seu modelo aparece no Código do Estúdio Visual com conteúdo semelhante ao abaixo:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Uma vez satisfeito com a sua configuração, guarde-a abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

Após alguns minutos, o modelo aparece sob o nó *Modelos.*

## <a name="deploy-the-model"></a>Implementar o modelo

No Código do Estúdio Visual, pode implementar o seu modelo como um serviço web para:

+ Instâncias do Contentor Azure (ACI).
+ Serviço Azure Kubernetes (AKS).

Não é necessário criar um recipiente ACI para testar com antecedência, porque os recipientes ACI são criados conforme necessário. No entanto, você precisa configurar os clusters AKS com antecedência. Para obter mais informações sobre as opções de implementação, consulte [os modelos de implementação com a Azure Machine Learning](how-to-deploy-and-where.md) .

Para implementar um serviço web como um ACI:

1. Na barra de atividades do Código do Estúdio Visual, selecione o ícone **Azure.** Aparece a vista Azure Machine Learning.
1. Expanda o seu nó de subscrição. 
1. Expandir o nó **teamWorkspace > Models.** 
1. Clique com o botão direito no **modelo MNIST-TensorFlow** e selecione **o Serviço de Implantação a partir do Modelo Registado.**

    > [!div class="mx-imgBorder"]
    > ![Implementar o modelo](./media/tutorial-train-deploy-image-classification-model-vscode/deploy-model.png)

1. Selecione **instâncias do contentor Azure**.
1. Nomeie o seu serviço "mnist-tensorflow-svc" e prima **Enter**.
1. Escolha o script para executar no recipiente pressionando **Insira** na caixa de entrada e navegando para o `score.py` ficheiro no `mnist-vscode-docs-sample` diretório.
1. Forneça as dependências necessárias para executar o script pressionando **Enter** na caixa de entrada e navegando para o `env.yml` ficheiro no `mnist-vscode-docs-sample` diretório.

    Um ficheiro que contenha as configurações do seu modelo aparece no Código do Estúdio Visual com conteúdo semelhante ao abaixo:

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

1. Uma vez satisfeito com a sua configuração, guarde-a abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

Neste momento, é enviado um pedido à Azure para implementar o seu serviço web. Este processo demora vários minutos. Uma vez implantado, o novo serviço aparece sob o nó *endpoints.*

## <a name="next-steps"></a>Passos seguintes

* Para uma passagem de como treinar com a Azure Machine Learning fora do Código do Estúdio Visual, consulte [Tutorial: Modelos de comboio com Azure Machine Learning](tutorial-train-models-with-aml.md).
* Para uma análise de como editar, correr e depurar código localmente, consulte o [tutorial python hello-world](https://code.visualstudio.com/docs/Python/Python-tutorial).