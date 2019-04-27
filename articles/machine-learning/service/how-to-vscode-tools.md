---
title: Utilizar o Visual Studio Code com
titleSuffix: Azure Machine Learning service
description: Saiba como instalar o Azure Machine Learning para Visual Studio Code e criar uma experimentação simples no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818749"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introdução ao Azure Machine Learning para Visual Studio Code

Neste artigo, ficará a saber como utilizar o **Azure Machine Learning para Visual Studio Code** extensão para preparar e implementar modelos com o serviço Azure Machine Learning no Visual Studio Code (VS Code) de aprendizagem profunda e de aprendizagem automática.

O serviço do Azure Machine Learning fornece suporte para a execução de experimentações localmente e destinos de computação remota. Para cada fase experimental, pode manter um registo de várias execuções, muitas vezes, o que precisar tentar iterativamente técnicas diferentes, hiperparâmetros e muito mais. Pode utilizar o Azure Machine Learning para controlar métricas personalizadas e experimente execuções, ativar a capacidade de reprodução de ciência de dados e auditability.

E pode implantar esses modelos para suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

+ Deve ser instalado o Visual Studio Code. VS Code é um editor de código fonte simples, poderoso, mas que é executado no seu ambiente de trabalho. Ele vem com suporte incorporado para o Python e muito mais.  [Saiba como instalar o VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Instalar o Python 3.5 ou superior](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Instalar o Azure Machine Learning para a extensão do VS Code

Ao instalar o **do Azure Machine Learning** extensão, duas extensões mais são instaladas automaticamente (se tiver acesso à internet). Eles estão o [conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) extensão e o [Python do Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python) extensão

Para trabalhar com o Azure Machine Learning, é necessário tornar o código VS num IDE de Python. Trabalhar com [Python no Visual Studio Code](https://code.visualstudio.com/docs/languages/python), requer a extensão de Python do Microsoft, que é instalada automaticamente com a extensão do Azure Machine Learning. A extensão faz um excelente IDE com código VS e funciona em qualquer sistema operativo com uma variedade de interprety de Python. Tira partido de tudo de energia do VS Code para fornecer o preenchimento automático e IntelliSense, linting, depuração e teste de unidade, juntamente com a capacidade de alternar facilmente entre ambientes do Python, incluindo virtual e os ambientes de conda. Confira estas noções básicas de editar, executar e depuração de código de Python, veja o [Python Hello World Tutorial](https://code.visualstudio.com/docs/python/python-tutorial)

**Para instalar a extensão do Azure Machine Learning:**

1. Inicie o VS Code.

1. Num browser, visite: [O Azure Machine Learning para Visual Studio Code (pré-visualização)](https://aka.ms/vscodetoolsforai) extensão

1. Na página da web, clique em **instalar**. 

1. No separador de extensão, clique em **instalar**.

1. Bem-vindo é aberto um separador no VS Code para a extensão e o símbolo do Azure (descrito na caixa vermelha na figura abaixo) é adicionado à barra de atividade.

   ![Ícone do Azure na barra de atividade do Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Na caixa de diálogo, clique em **sessão** e siga o pedido na tela para autenticar com o Azure. 
   
   A extensão da conta do Azure, que foi instalada, juntamente com o Azure Machine Learning para a extensão do VS Code, ajuda-o a autenticar com a sua conta do Azure. Ver a lista de comandos do [extensão da conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) página.

> [!Tip] 
> Veja a [IntelliCode extensão para o VS Code (pré-visualização)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fornece um conjunto de capacidades de IA auxiliado para IntelliSense em Python, tais como de inferir os auto-conclusões mais relevantes com base no contexto de código atual.

## <a name="azure-ml-sdk-installation"></a>Instalação do SDK do Azure ML

1. Certifique-se de que o Python 3.5 ou superior está instalado e reconhecido pelo código VS. Se instalar a aplicação agora, em seguida, reinicie o VS Code e selecione um interpretador de Python com as instruções apresentadas em https://code.visualstudio.com/docs/python/python-tutorial.

1. Na janela de terminal integrada, especifique o interpretador de Python a utilizar ou pode usar **Enter** para utilizar o interpretador de Python padrão.

   ![Selecione o interpretador](./media/vscode-tools-for-ai/python.png)

1. No canto inferior direito da janela, uma notificação será exibida indicando que o Azure ML SDK está a ser instalado automaticamente.    É criado um ambiente de Python privado local que tem os pré-requisitos do Visual Studio Code para trabalhar com o serviço Azure Machine Learning.

   ![instalar o SDK do Azure Machine Learning para o Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Antes de começar a formação e implementar modelos de aprendizagem automática com o VS Code, tem de criar uma [Azure Machine Learning a área de trabalho de serviço](concept-azure-machine-learning-architecture.md#workspace) na cloud para conter os seus modelos e recursos. Saiba como criar uma e criar a sua primeira experiência nessa área de trabalho.

1. Clique no ícone do Azure na barra de atividade de código do Visual Studio. É apresentada a barra lateral do Azure Machine Learning.

   [![install](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. A subscrição do Azure com o botão direito e selecione **criar área de trabalho**. É apresentada uma lista. Na imagem animada, o nome da subscrição é "Versão de avaliação" e a área de trabalho é 'TeamWorkspace'. 

1. Selecione um grupo de recursos existente na lista ou crie um novo utilizando o assistente na paleta de comandos.

1. No campo, escreva um nome exclusivo e claro para sua nova área de trabalho. Nas capturas de ecrã, a área de trabalho com o nome 'TeamWorkspace'.

1. Prima enter e a nova área de trabalho é criada. Ele aparece na árvore abaixo do nome de subscrição.

1. Com o botão direito no nó de experimentação e escolha **experimentação criar** no menu de contexto.  Experimentações manter o controle de suas execuções com o Azure Machine Learning.

1. No campo, introduza um nome de sua experiência. Nas capturas de ecrã, a experimentação com o nome 'MNIST'.
 
1. Prima enter e é criada a nova experimentação. Ele aparece na árvore abaixo do nome de área de trabalho.

1. Pode com o botão direito numa experimentação numa área de trabalho e selecione "Definir como experimentação Active Directory". O **'Active'** experimentação é a experimentação estiver a utilizar atualmente e sua Abrir pasta no VS Code vai ser ligada a esta experiência na cloud. Esta pasta deve conter os seus scripts de Python locais.

   Agora cada da sua experimentação é executada com a sua experimentação, para que todas as métricas chave serão armazenadas no histórico de experimentação e os modelos de que preparar, irão obter automaticamente carregados para o Azure Machine Learning e armazenados com a sua experimentação métricas e registos.

   [![Anexar uma pasta no VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Criar e gerir os destinos de computação

Com o Azure Machine Learning para o VS Code, pode preparar os dados, formar modelos e implementá-las tanto localmente como destinos de computação remota.

Esta extensão oferece suporte a vários destinos de computação remota diferentes para o Azure Machine Learning. Consulte a [uma lista completa de destinos de computação suportados](how-to-set-up-training-targets.md) para o Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Criar destinos de computação do Azure Machine Learning no VS Code

**Para criar um destino de computação:**

1. Clique no ícone do Azure na barra de atividade de código do Visual Studio. É apresentada a barra lateral do Azure Machine Learning.

2. Na vista de árvore, expanda a sua subscrição do Azure e a área de trabalho do serviço Azure Machine Learning. Na imagem animada, o nome da subscrição é "Versão de avaliação" e a área de trabalho é 'TeamWorkspace'. 

3. Sob o nó de área de trabalho, clique com botão direito a **computação** nó e escolha **criar computação**.

4. Escolha o tipo de destino de computação da lista. 

5. Na paleta de comandos, selecione um tamanho de Máquina Virtual.

6. Na paleta de comandos, introduza um nome para o destino de computação no campo. 

7. Especifique quaisquer propriedades avançadas no ficheiro de configuração JSON que se abre num novo separador. Pode especificar as propriedades, como uma contagem máxima de nós.

8. Quando tiver concluído a configurar o destino de computação, clique em **submeter** no canto inferior direito da tela.

Eis um exemplo para a criação de uma computação do Azure Machine Learning (AMLCompute): [![Criar AML computação no VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>O ficheiro de configuração de execução

A extensão do VS Code criará automaticamente um destino de computação local e executar configurações para a sua **local** e **docker** ambientes no seu computador local. Os ficheiros de configuração de execução podem ser encontrados no nó de destino de computação associado. 

## <a name="train-and-tune-models"></a>Dar formação e otimizar modelos

Utilize o Azure Machine Learning para o VS Code (pré-visualização) para rapidamente reanalisa o seu código, siga os passos e depurar e utilizar a solução de controlo de código de origem à escolha. 

**Para executar localmente a sua experimentação com o Azure Machine Learning:**

1. Clique no ícone do Azure na barra de atividade de código do Visual Studio. É apresentada a barra lateral do Azure Machine Learning.

1. Na vista de árvore, expanda a sua subscrição do Azure e a área de trabalho do serviço Azure Machine Learning. 

1. Sob o nó de área de trabalho, expanda o **de computação** nó e o botão direito do mouse sobre o **executar configuração** de computação que pretende utilizar. 

1. Selecione **execute experimentação**.

1. Selecione o script a executar a partir do Explorador de ficheiros. 

1. Clique em **veja a execução da experimentação** para ver o portal do Azure Machine Learning integrado para monitorizar as execuções e ver os modelos de formação.

Eis um exemplo para a execução de uma experimentação localmente: [![Uma experimentação a executar localmente](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Utilizar computações remotas para experimentações no VS Code

Para utilizar um destino de computação remota ao treinar, terá de criar um ficheiro de configuração de execução. Este ficheiro diz ao Azure Machine Learning não só onde executar a sua experimentação, mas também como preparar o ambiente.

#### <a name="the-conda-dependencies-file"></a>O ficheiro de dependências de conda

Por predefinição, é criado um novo ambiente de conda para e suas dependências de instalação são geridas. No entanto, tem de especificar as suas dependências e suas versões no `aml_config/conda_dependencies.yml` ficheiro. 

Este é um trecho da predefinição "aml_config/conda_dependencies.yml'. Por exemplo, pode especificar "tensorflow = 1.12.0' como mostrado abaixo. Se não especificar a versão da dependência, em seguida, a versão mais recente será utilizada.  
Pode adicionar dependências adicionais no ficheiro de configuração.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**Para executar a experimentação com o Azure Machine Learning no remoto destino de computação:**

1. Clique no ícone do Azure na barra de atividade de código do Visual Studio. É apresentada a barra lateral do Azure Machine Learning.

1. Na vista de árvore, expanda a sua subscrição do Azure e a área de trabalho do serviço Azure Machine Learning. 

1. Com o botão direito no seu script de python na janela do editor e selecione **AML: Executar como experimentação no Azure**. 

1. Na paleta de comandos, selecione o destino de computação. 

1. Na paleta de comandos, introduza o nome de configuração de execução no campo. 

1. Edite o ficheiro de conda_dependencies.yml para especificar dependências de tempo de execução da experimentação, em seguida, clique em **submeter** no canto inferior direito da tela. 

1. Clique em **veja a execução da experimentação** para ver o portal do Azure Machine Learning integrado para monitorizar as execuções e ver os modelos de formação.

Eis um exemplo para a execução de uma experimentação num destino de computação remota: [![Execução num destino remoto](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Implementar e gerir modelos
O Azure Machine Learning permite implementar e gerir os seus modelos de machine learning na cloud e no edge. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registar o seu modelo para o Azure Machine Learning a partir do código de VS

Agora que tem a formação seu modelo, pode registá-lo na sua área de trabalho.
Modelos de registado podem ser monitorizados e implementados.

**Para registar o seu modelo:**

1. Clique no ícone do Azure na barra de atividade de código do Visual Studio. É apresentada a barra lateral do Azure Machine Learning.

1. Na vista de árvore, expanda a sua subscrição do Azure e a área de trabalho do serviço Azure Machine Learning.

1. Sob o nó de área de trabalho, clique com botão direito **modelos** e escolha **modelo registar**.

1. Na paleta de comandos, introduza um nome de modelo no campo. 

1. Na lista, escolha se pretende carregar um **ficheiro de modelo** (para modelos de únicos) um **pasta modelo** (para modelos com vários ficheiros, como o Tensorflow). 

1. Selecione o ficheiro ou pasta.

1. Quando tiver concluído a configurar as propriedades de seu modelo, clique em **submeter** no canto inferior direito da tela. 

Eis um exemplo para registar o seu modelo de AML: [![Registar um modelo de AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>Implementar o seu serviço a partir do código VS

Utilizar o VS Code, pode implementar o serviço web para:
+ Instância de contentor do Azure (ACI): para fins de teste
+ O Azure Kubernetes Service (AKS): para produção 

Não é necessário criar um contentor do ACI para testar com antecedência, uma vez que são criados dinamicamente. No entanto, os clusters do AKS é necessário ser configurado com antecedência. 

Saiba mais sobre [implementação com o Azure Machine Learning](how-to-deploy-and-where.md) em geral.

**Para implementar um serviço web:**

1. Clique no ícone do Azure na barra de atividade de código do Visual Studio. É apresentada a barra lateral do Azure Machine Learning.

1. Na vista de árvore, expanda a sua subscrição do Azure e a sua área de trabalho do serviço do Azure Machine Learning.

1. Sob o nó de área de trabalho, expanda o **modelos** nó.

1. Com o botão direito do modelo que pretende implementar e escolha **implementar o serviço do modelo registado** comando no menu de contexto.

1. Na paleta de comandos, escolha o destino de computação para a implementação da lista. 

1. Na paleta de comandos, introduza um nome para este serviço no campo.  

1. Na paleta de comandos, prima a tecla Enter no teclado para procurar e selecione o ficheiro de script.

1. Na paleta de comandos, prima a tecla Enter no teclado para procurar e selecione o ficheiro de dependências de conda.

1. Quando tiver concluído a configurar as propriedades de seu serviço, clique em **submeter** no canto inferior direito do ecrã, para implementar. Nesse arquivo de propriedades do serviço, pode especificar um ficheiro de Docker local ou um ficheiro de schema.json que pretende utilizar.

O serviço web agora é implementado.

Eis um exemplo para implementar um serviço web: [![Implementar um serviço web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Utilizar atalhos de teclado

Como a maioria do VS Code, os recursos do Azure Machine Learning no VS Code são acessíveis a partir do teclado. A combinação de teclas mais importante saber é Ctrl + Shift + P, que abrirá a paleta de comandos. A partir daqui, tem acesso a todas as funcionalidades do VS Code, incluindo atalhos de teclado para as operações mais comuns.

[![Atalhos de teclado para o Azure Machine Learning para o VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passos Seguintes

Para um passo a passo de treinamento com o Machine Learning fora do VS Code, leia [Tutorial: Utilizar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).

Para um passo a passo de editar, executar e depurar o código localmente, consulte o [Python Hello World Tutorial](https://code.visualstudio.com/docs/python/python-tutorial)
