---
title: Usar Visual Studio Code para aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Saiba como instalar Azure Machine Learning para Visual Studio Code e criar um experimento no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: a93c71a97cdb1f6296919a248cf7ef545f7b307f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269244"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introdução ao Azure Machine Learning para Visual Studio Code

Neste artigo, você aprenderá a usar o **Azure Machine Learning para Visual Studio Code** extensão para treinar e implantar modelos de aprendizado de máquina.

O [serviço de Azure Machine Learning](overview-what-is-azure-ml.md) simplifica a criação, o treinamento e a implantação de modelos de aprendizado de máquina.
+ Para treinamento, ele fornece suporte para a execução de experimentos locais ou remotamente. Para cada experimento, você pode registrar métricas personalizadas de várias execuções para ajustar hiperparâmetros
+ Você também pode usar o serviço de Azure Machine Learning para implantar facilmente modelos de aprendizado de máquina para suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

+ Instale o [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), um editor de código leve que é executado no Windows, Mac e Linux.

+ [Instale o Python 3,5 ou posterior](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>Instalar a extensão

Quando você instala a extensão de Azure Machine Learning, mais duas extensões são instaladas automaticamente. Eles são a [extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e a [extensão Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). Para obter mais informações sobre como usar a extensão do Python para editar, executar e depurar o código Python, consulte o [tutorial do Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).

Para instalar a extensão de Azure Machine Learning:

1. Abra o Visual Studio Code.

1. Alterne para a guia extensões e procure "Azure Machine Learning".

1. Na guia extensão, selecione **instalar**.

1. Uma guia de boas-vindas para a extensão é aberta no Visual Studio Code e o símbolo do Azure (realçado em vermelho na captura de tela a seguir) é adicionado à barra de atividades.

   ![Ícone do Azure na barra de atividades do Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Na caixa de diálogo, selecione **entrar** e siga os prompts para autenticar com o Azure.

   A extensão de conta do Azure, que foi instalada junto com a Azure Machine Learning para Visual Studio Code extensão, ajuda você a se autenticar com sua conta do Azure. Para obter uma lista de comandos, consulte a página da [extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!TIP]
> Você também pode baixar o instalador de extensão diretamente de [Azure Machine Learning para extensão de Visual Studio Code (versão prévia)](https://aka.ms/vscodetoolsforai).

## <a name="quickstart-with-azure-machine-learning"></a>Início rápido com Azure Machine Learning
Há várias maneiras de executar seus scripts de treinamento usando o serviço de Azure Machine Learning. Se você estiver apenas começando, vamos primeiro percorrer como enviar rapidamente um script de treinamento para ser executado no Azure.

Se você já está mais familiarizado com os conceitos de Azure Machine Learning e gostaria de obter mais detalhes sobre como gerenciá-los e usá-los com a extensão, consulte [Azure Machine Learning detalhadamente com vs Code](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) abaixo.

## <a name="run-an-existing-python-training-script-in-azure"></a>Executar um script de treinamento do Python existente no Azure
Se você tiver um script de treinamento existente, a extensão Azure Machine Learning para VS Code não só fornecerá uma excelente experiência de edição, depuração e gerenciamento de código-fonte, mas também facilita a execução e a armazena de métricas para o script no Azure.

Vamos começar. Você pode usar seu próprio script de treinamento se ele estiver pronto ou clonar o [repositório vscode-Tools-for-ai](https://github.com/microsoft/vscode-tools-for-ai)de exemplo. Este é o repositório público para o arquivamento de problemas sobre esta extensão. Ele também contém uma pequena pasta de exemplo **mnist** que usaremos para este exemplo.

1. Abra a pasta **mnist** em vs Code.

1. Crie um novo ambiente do Python usando seu pacote de ambiente virtual favorito ou Anaconda e instale os pacotes TensorFlow e numpy.

1. Selecione o novo ambiente criado como seu interpretador do Python no canto inferior esquerdo da barra de status do VS Code.

1. Abra o **Train.py** e execute-o abrindo o depurador e pressionando o botão Executar (ou apenas pressione F5).

   [![Executar treinamento do MNIST](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

Se tudo estiver instalado corretamente, o script será executado e criará um modelo TensorFlow na pasta Outputs.

[![Mostrar modelo de TensorFlow](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

Agora que você sabe que o script é executado corretamente, vamos executá-lo no Azure!

Isso pode ser feito facilmente sem nenhuma modificação extra do **Train.py**. No entanto, com apenas algumas alterações simples, você pode fazer uso de Azure Machine Learning para controlar automaticamente as métricas importantes de sua escolha sobre cada execução de treinamento.

### <a name="make-azure-aware-of-your-run-metrics"></a>Tornar o Azure ciente de suas métricas de execução
Para modificar seu projeto para que o Azure possa se tornar ciente de informações importantes em suas execuções:

1. Crie um arquivo chamado **amlrun.py** na mesma pasta que **Train.py**

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. Importar o arquivo amlrun no **Train.py**

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. Inicializar o objeto de execução em **Train.py**

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Métricas de log para o Azure com a função Run. log ():

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Executar o script no Azure
Já está! Agora, basta usar a extensão para executar o script na nuvem! Observe que o vídeo explicativo a seguir usa a liberdade de compactar a quantidade de tempo que leva para criar um novo espaço de trabalho e computação do Azure ML, bem como o tempo necessário para executar o script de treinamento.

   [![Iniciar um experimento do Azure ML](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

Depois de clicar no botão Executar experimento, responda aos prompts da seguinte maneira:

1. Escolher a sua subscrição do Azure
1. Escolha criar um *novo* espaço de trabalho do Azure ml
1. Escolha um conjunto de modelos pré-configurados para inicializar o ambiente do Python para as execuções. Os modelos fornecem um ponto de partida e incluem configurações para:
    1. **PyTorch**, **TensorFlow**ou **Scikit-Learn**
    1. Treinamento de computação **única** ou **distribuída**
    1. **Geral** para ambientes personalizados
1. Verifique se a lista de pacotes Pip e Conda está completa para o seu script adicionando todos os pacotes que não estão incluídos no modelo.
1. Examine os nomes e as especificações padrão para a execução do experimento e clique no link **Enviar experimento** no arquivo JSON. O arquivo JSON não será salvo, pois simplesmente você poderá revisar ou alterar as configurações de experimento antes do envio.
1. Sente-se e relaxe enquanto a extensão define tudo para você e executa seu script!

    [![Treinar na nuvem](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

Em alguns segundos, você será notificado de que o experimento foi enviado para o Azure no momento em que você pode exibir seu progresso na portal do Azure clicando no link **Exibir execução de experimento** na notificação de vs Code ou dentro vs Code ao pressionar a atualização na guia Azure.

No momento, só há suporte para a exibição de métricas de execução no portal do Azure. O link **Exibir teste de execução** mencionado acima levará você para a execução onde você verá as métricas que você registrou.
[![Execução do experimento no portal](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Azure Machine Learning detalhadamente com VS Code

No passo a passos acima, enviamos um experimento seguindo o caminho mais fácil. Como você deve ter notado, a extensão minimiza as etapas que você precisa gerenciar para executar um experimento. Nesta seção, abordaremos como você pode gerenciar todos os conceitos de Azure Machine Learning individualmente, fornecendo o máximo de controle.

Antes de começar a treinar e implantar modelos de aprendizado de máquina no Visual Studio Code, você precisa criar um [espaço de trabalho Azure Machine Learning](concept-workspace.md) na nuvem. Este espaço de trabalho conterá seus modelos e recursos.

### <a name="create-a-workspace"></a>Criar uma área de trabalho

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

    [![Criar um espaço de trabalho](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. Clique com o botão direito do mouse em sua assinatura do Azure e selecione **criar espaço de trabalho**. Por padrão, um nome é gerado contendo a data e hora de criação. Altere o nome para **TeamWorkspace** e pressione Enter.

1. Selecione um grupo de recursos na lista se você souber qual escolher ou criar um novo. Se estiver criando um novo, escolha um local mais próximo do local em que você planeja implantar seu modelo. Nesse caso, escolhemos o **oeste dos EUA 2**.

1. Depois de pressionar Enter, Azure Machine Learning receberá a solicitação para criar seu espaço de trabalho. Você será notificado sobre o processo na área de notificação Visual Studio Code.

1. Expanda o nó assinatura para localizar seu espaço de trabalho recém-criado.

### <a name="create-an-experiment"></a>Criar uma experimentação
Um ou mais experimentos podem ser criados em seu espaço de trabalho para rastrear e analisar execuções de treinamento de modelo individual. As execuções podem ser feitas na nuvem do Azure ou em seu computador local.

1. Expanda o espaço de trabalho **TeamWorkspace** . Clique com o botão direito do mouse no nó **experimentos** e escolha **criar experimento** no menu de contexto.

1. No prompt, insira um nome para o experimento. Nas capturas de tela de exemplo, o experimento é denominado **MNIST**.

1. Selecione Enter para criar o novo experimento. O novo experimento aparece na árvore como um filho do nó **experimentos** .

1. Em um espaço de trabalho, você pode clicar com o botão direito do mouse em um experimento para defini-lo como o experimento **ativo** . Os links de experimento **ativas** que experimentam na nuvem para a pasta que você abriu no momento Visual Studio Code. Essa pasta deve conter seus scripts Python locais. Ao definir um experimento ativo, as principais métricas para todas as execuções de treinamento serão armazenadas no experimento, independentemente de onde elas forem executadas.

    [![Criar um experimento](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>Criar e gerir os destinos de computação

Com Azure Machine Learning para Visual Studio Code, você pode preparar seus dados, treinar modelos e implantá-los localmente e em destinos de computação remota.

A extensão dá suporte a vários destinos de computação remota para Azure Machine Learning. Para obter mais informações, consulte a lista completa de [destinos de computação com suporte para Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Criar destinos de computação para Azure Machine Learning no Visual Studio Code

Para criar um destino de computação:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e Azure Machine Learning espaço de trabalho.

1. Sob o nó de área de trabalho, clique com botão direito a **computação** nó e escolha **criar computação**.

1. Escolha o tipo de destino de computação da lista.

1. No prompt da paleta de comandos, selecione um tamanho de máquina virtual. Você pode filtrar as computações com texto, como "GPU".

1. No prompt da paleta de comandos, insira um nome para o destino de computação.

1. Depois de inserir o nome, a computação será criada usando parâmetros padrão. Para alterar os parâmetros, clique com o botão direito do mouse na nova computação e escolha **Editar computação**.

1. No JSON que é exibido, faça as alterações desejadas e clique no CodeLens "salvar e continuar" (usando o teclado você pode pressionar **Ctrl-Shift-p** para invocar a paleta de **comandos e executar o Azure ml: Comando Save and** continue)

Aqui está um exemplo de como criar e editar um AMLCompute (computação de Azure Machine Learning):

[![Criar computação AML no Visual Studio Code](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>O arquivo de configuração de execução

Para executar um experimento de Azure Machine Learning em uma computação, essa computação precisa ser configurada adequadamente. Um arquivo de configuração de execução é o mecanismo pelo qual esse ambiente é especificado.

Aqui está um exemplo de como criar uma configuração de execução para o AmlCompute, criado acima.

[![Criar uma configuração de execução para uma computação](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

Para executar experimentos do Azure ML no computador local, um arquivo de configuração de execução ainda é necessário. Ao criar uma configuração de execução local, o ambiente Python usado usará como padrão o caminho para o intérprete que você definiu dentro VS Code.

### <a name="train-and-tune-models"></a>Dar formação e otimizar modelos

Usando a extensão do am do Azure para VS Code há várias maneiras de executar um script de treinamento em um experimento.

1. Clique com o botão direito do mouse no **script de treinamento e escolha Azure ml: Executar como experimento no Azure**
1. Clique no ícone da barra de ferramentas executar experimento.
1. Clique com o botão direito do mouse em um nó de configuração de execução.
1. Use a paleta de comandos vs Code para **executar o Azure ml: Executar experimento**

Para executar um experimento de Azure Machine Learning:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e Azure Machine Learning espaço de trabalho.

1. No nó do espaço de trabalho, expanda o nó **experimentos** e clique com o botão direito do mouse no experimento que você deseja executar.

1. Selecione **execute experimentação**.

1. Escolha o nome do arquivo Python que você deseja executar para treinar o modelo e pressione ENTER para enviar a execução. Nota: O arquivo escolhido deve residir na pasta que você abriu no momento VS Code.

1. Depois que a execução for enviada, um **nó de execução** será exibido abaixo do experimento escolhido. Use este nó para monitorar o estado de suas execuções. Nota: Pode ser necessário atualizar periodicamente a janela para ver o status mais recente.

Aqui está um exemplo de como executar um experimento na computação criada anteriormente:

[![Executar um experimento localmente](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Implementar e gerir modelos
No Azure Machine Learning, você pode implantar e gerenciar seus modelos de aprendizado de máquina na nuvem e na borda.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrar seu modelo para Azure Machine Learning de Visual Studio Code

Agora que você treinou seu modelo, você pode registrá-lo em seu espaço de trabalho. Você pode acompanhar e implantar modelos registrados.

Para registrar seu modelo:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e Azure Machine Learning espaço de trabalho.

1. Sob o nó de área de trabalho, clique com botão direito **modelos** e escolha **modelo registar**.

1. Na paleta de comandos, no campo, insira um nome de modelo.

1. Na lista, escolha se deseja carregar um **arquivo de modelo** (para modelos únicos) ou uma **pasta de modelo** (para modelos com vários arquivos, como TensorFlow).

1. Selecione o ficheiro ou pasta.

1. Quando terminar de configurar as propriedades do modelo, no canto inferior direito da janela, selecione **Enviar**.

Aqui está um exemplo de como registrar seu modelo para Azure Machine Learning:

[![Registrando um modelo para AML](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Implantar seu serviço de Visual Studio Code

No Visual Studio Code, você pode implantar seu serviço Web para:
+ ACI (instâncias de contêiner do Azure) para teste.
+ AKS (serviço kubernetes do Azure) para produção.

Você não precisa criar um contêiner ACI para testar com antecedência, pois os contêineres ACI são criados conforme necessário. No entanto, você precisa configurar clusters AKS com antecedência. Para obter mais informações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

Para implantar um serviço Web:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. A barra lateral Azure Machine Learning é exibida.

1. No modo de exibição de árvore, expanda sua assinatura do Azure e seu espaço de trabalho Azure Machine Learning.

1. Sob o nó de área de trabalho, expanda o **modelos** nó.

1. Clique com o botão direito do mouse no modelo que você deseja implantar e escolha **implantar serviço do modelo registrado** no menu de contexto.

1. Na paleta de comandos, escolha o destino de computação no qual você deseja implantar.

1. Na paleta de comandos, no campo, insira um nome para esse serviço.

1. Na paleta de comandos, selecione a tecla Enter no teclado para procurar e selecionar o arquivo de script.

1. Na paleta de comandos, selecione a tecla Enter no teclado para procurar e selecionar o arquivo de dependência Conda.

1. Quando terminar de configurar suas propriedades de serviço, no canto inferior direito da janela, selecione **Enviar** para implantar. No arquivo de propriedades do serviço, você pode especificar um arquivo do Docker local ou um arquivo Schema. JSON.

O serviço web agora é implementado.

Veja um exemplo de como implantar um serviço Web:

[![Implantar um serviço Web](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Experimente recursos adicionais

Você pode usar a paleta de comandos para acessar muitos recursos Azure Machine Learning no Visual Studio Code. Para invocar o tipo de paleta de comandos Ctrl + Shift + P. A partir daqui, você pode pesquisar recursos adicionais do Azure ML da extensão.

[![Atalhos de teclado para Azure Machine Learning Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma explicação de como treinar com Azure Machine Learning fora do Visual Studio Code, consulte [o tutorial: Treine modelos com Azure Machine Learning](tutorial-train-models-with-aml.md).
* Para obter uma explicação de como editar, executar e depurar código localmente, consulte o [tutorial do Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).
