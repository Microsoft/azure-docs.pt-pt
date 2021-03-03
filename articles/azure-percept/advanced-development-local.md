---
title: Começar com o desenvolvimento avançado do Azure Percept localmente
description: Começar com o desenvolvimento local de machine learning usando VS Code + Jupyter Notebooks on AzureML
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665113"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Começar com o desenvolvimento de machine learning usando VS Code + Jupyter Notebooks on AzureML

Este artigo acompanha-o através do processo de criação de um espaço de trabalho de Aprendizagem automática Azure, criando uma instância de computação, criando um ambiente de desenvolvimento do Código do Estúdio Visual na sua máquina local, e executando as células de uma amostra pré-configurada do caderno Jupyter dentro do Código VS.

O [portátil](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) realiza a aprendizagem de transferência utilizando um modelo tensorFlow pré-treinado (MobileNetSSDV2Lite) em AzureML em Python com um conjunto de dados personalizado para detetar tigelas.

O portátil mostra como começar a partir do conjunto de [dados COCO,](https://cocodataset.org/#home)filtre-o apenas para a classe de interesse (tigelas) e, em seguida, convertê-lo no formato apropriado. Em alternativa, pode utilizar a ferramenta de rotulagem [VoTT 2](https://github.com/microsoft/VoTT) de código aberto para criar e rotular caixas de delimitação no formato PASCAL VOC.

Depois de retreinar o modelo no conjunto de dados personalizado, o modelo pode ser implantado no seu Azure Percept DK utilizando o método de atualização dupla do módulo. Em seguida, pode verificar a inferencing do modelo visualizando o fluxo RTSP ao vivo do Azure Percept Vision SoM. Tanto a reconversão do modelo como a implementação são realizadas dentro do caderno através da sua instância de computação remota.

## <a name="prerequisites"></a>Pré-requisitos

- [Assinatura de aprendizagem automática Azure](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK com Azure Percept Vision SoM conectado](./overview-azure-percept-dk.md)
- [Experiência de embarque Azure Percept DK](./quickstart-percept-dk-set-up.md) concluída

## <a name="download-azure-percept-github-repository"></a>Baixar o repositório Azure Percept GitHub

1. Vá ao [repositório Azure Percept DK GitHub.](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview)
1. Clone o repositório ou descarregue o ficheiro ZIP. Perto da parte superior do ecrã, clique em **Código**  ->  **Descarregamento ZIP**.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="Ecrã de descarregamento do GitHub.":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Criar um espaço de trabalho de aprendizagem de máquinas Azure e instância de computação remota

1. Vá ao [Portal de Aprendizagem automática Azure.](https://ml.azure.com)
1. Selecione o seu diretório, subscrição Azure e espaço de trabalho machine learning a partir dos menus drop down e clique **em Começar**.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Azure ML começa o ecrã.":::

    Se ainda não tiver um espaço de trabalho para aprendizagem automática Azure, clique em **Criar um novo espaço de trabalho.** No novo separador do navegador, faça o seguinte:

    1. Selecione a sua subscrição do Azure.
    1. Selecione o seu grupo de recursos.
    1. Insira um nome para o seu espaço de trabalho.
    1. Selecione a sua região.
    1. Selecione a sua edição do espaço de trabalho.
    1. Clique em **Rever e criar**.
    1. Na página seguinte, reveja as suas seleções e clique em **Criar**.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Tela de criação de espaço de trabalho em Azure ML.":::

    Por favor, permita alguns minutos para a criação do espaço de trabalho. Após a criação do espaço de trabalho estar concluída, verá marcas de verificação verdes ao lado dos seus recursos e a **sua implantação está completa** no topo da página de visão geral dos Serviços de Aprendizagem automática.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Confirmação da criação do espaço de trabalho." lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    Assim que a sua criação de espaço de trabalho estiver concluída, volte ao separador portal de machine learning e clique em **Iniciar**.

1. Na página inicial do espaço de trabalho de aprendizagem automática, clique em **Compute** no painel da esquerda.

1. Se não houver uma instância computacional existente, crie um novo cpu ou cálculo gpu clicando em **New**. Na janela de instância de **computação Nova,** introduza um **nome Compute,** escolha um **tipo de máquina Virtual** e selecione um tamanho de máquina **Virtual**. Clique em **Criar**.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Novo ecrã de criação de instância compute.":::

    Uma vez clicada em **Criar,** demora alguns minutos a criar a instância de cálculo. O seu estado **de Computação** apresentará um círculo verde e **\<your compute name> - Correr** após a criação do cálculo está completo. Esta instância compute executa o servidor Jupyter e será alavancada para este tutorial.

## <a name="visual-studio-code-development-environment-setup"></a>Configuração do ambiente de desenvolvimento do código do estúdio visual

1. Instale as ferramentas necessárias.

    1. Opção 1:

        Utilize o [Instalador Dev Tools Pack](./dev-tools-installer.md) para instalar as seguintes embalagens:

        - Visual Studio Code
        - Python 3.5, 3.6 ou 3.7
        - Miniconda3
        - Intel OpenVino Toolkit 2020.2

        Nota: O Intel OpenVino Toolkit está listado como um pacote opcional no Instalador Dev Tools Pack, mas é necessário para trabalhar com o SoM SoM Azure Percept Vision do Azure Percept DK Development Kit.

    1. Opção 2:

        Se preferir não utilizar o Instalador Dev Tools Pack, instale manualmente as seguintes embalagens clicando nos links abaixo e seguindo as diretrizes de descarregamento e instalação especificadas:

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3.5, 3.6 ou 3.7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel OpenVino Toolkit 2020.2](https://docs.openvinotoolkit.org/)

    Nota: Se já tiver a distribuição completa da Anaconda instalada, não precisa de instalar o Miniconda. Em alternativa, se preferir não utilizar Anaconda ou Miniconda, pode criar um ambiente virtual Python e instalar os pacotes necessários para executar o desenvolvimento do seu modelo de IA utilizando pip.

1. Abra o Visual Studio Code.
1. Criar um ambiente de ciência de dados:

    - Opção 1 - Ligue-se a uma instância de computação remota existente ou nova que já tem os pacotes ML curados. **Esta é a opção que iremos usar neste tutorial.**

    - Opção 2 - Configurar um ambiente conda numa máquina local.
        1. Abra um pedido de comando Anaconda ou Miniconda e executar o seguinte comando para criar um ambiente chamado **myenv** com os pacotes especificados:

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Para obter informações adicionais sobre a criação e gestão de ambientes Daconda, consulte a documentação da [Anaconda.](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)

1. Criar um espaço de trabalho vs Code:

    1. Crie uma pasta num local conveniente para servir como espaço de trabalho do Código do Estúdio Visual. Dê-lhe **o meu espaço de trabalho.**
    1. Abra **o meu espaço de trabalho** no Código do Estúdio Visual clicando **na** pasta de  >  seleção de  >  **ficheiros.**
    1. No explorador de ficheiros, navegue para e selecione o [ficheiro Transferlearningusing_SSDLiteV2 Model.ipynbb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) a partir da sua cópia local do repositório Azure Percept DK GitHub. Copie este ficheiro de caderno para a pasta myworkspace.

## <a name="azure-integration-with-visual-studio-code"></a>Integração do Azure com o Código do Estúdio Visual

1. Se ainda não o fez, inscreva-se na [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

1. Instale as seguintes extensões Azure para o Código VS:
    - [Extensão de aprendizagem automática Azure](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).
    - Extensão Python Insiders. Em Código VS, vá à **Paleta de**  ->  **Comando Ver.** Na paleta de comando, insira e selecione **Python: Switch to Insiders Daily Channel**.
    - [Extensão da Conta Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Recarregue a sua janela no Código VS quando solicitado.
    - [Extensão Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Extensão Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Inscreva-se na sua Conta Azure dentro do Código do Estúdio Visual para obter recursos e executar cargas de trabalho no Azure.
    1. Abra a paleta de comando no Código do Estúdio Visual selecionando **a** Paleta de Comando  >  **ver** a partir da barra de menu.
    1. Insira **Azure: Inicie a sessão na** paleta de comando para iniciar o processo de login.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Sinal de azul no ecrã." lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Ative a extensão Python e a conta Azure clicando no ícone Azure no lado esquerdo do Código VS.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Ícone azul no código VS.":::

    1. Abra o caderno Transferlearningusing_SSDLiteV2 Model_VSCode.ipynb da pasta **myworkspace.**
    1. Abra a paleta de comando. Introduza e selecione **Python: especifique o servidor Jupyter local ou remoto para ligações**.
    1. Deve ver uma lista de opções de conexão à escolha. Selecione **Azure ML Compute Instances**.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Opções de instância de computação Azure ML em Código VS.":::

    1. Siga as instruções guiadas para escolher uma subscrição, espaço de trabalho e instância de computação remota. Selecione o espaço de trabalho e a instância de computação remota criada anteriormente neste tutorial. Também tem a opção de criar uma nova instância computacional.
    1. Depois de selecionar uma instância de cálculo, será solicitado para recarregar a sua janela vs Code. Uma vez recarregar, selecione o núcleo **Python 3.6 - AzureML.** Agora pode executar qualquer uma das células do seu caderno. Executar uma célula de portátil instantaneamente a ligação entre o seu caderno e a sua instância de computação. A barra de ferramentas do portátil será atualizada para refletir a instância computacional em que está a trabalhar.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="Seleção kernel em Código VS." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Trabalhando com o caderno

Está agora pronto para executar o caderno para treinar o seu detetor de taças personalizado no Código VS e implantá-lo no seu devkit. Certifique-se de que executa cada célula do caderno individualmente, uma vez que algumas das células requerem parâmetros de entrada antes de executar o script. As células que necessitam de parâmetros de entrada podem ser editadas diretamente no caderno. Para executar uma célula, clique no ícone de reprodução no lado esquerdo da célula:

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Portátil realçando ícone de célula.":::

## <a name="next-steps"></a>Passos seguintes

Para mais cadernos de exemplo de serviço de aprendizagem automática Azure, visite este [repo.](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)
