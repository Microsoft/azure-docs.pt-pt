---
title: Utilizar o Visual Studio Code para machine learning
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
ms.openlocfilehash: 19873256f8253fff75cfd42df7b876106a9e98e5
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464795"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Introdução ao Azure Machine Learning para Visual Studio Code

Neste artigo, aprenderá a utilizar a extensão para o Azure Machine Learning para Visual Studio Code para preparar e implementar o machine learning e modelos de aprendizagem através do serviço Azure Machine Learning no Visual Studio Code.

O serviço Azure Machine Learning fornece suporte para experimentações que executar localmente e destinos de computação remota. Para cada fase experimental, pode manter um registo de várias execuções, muitas vezes, o que precisar tentar iterativamente técnicas diferentes, hiperparâmetros e muito mais. Pode utilizar o Azure Machine Learning para controlar métricas personalizadas e experimente execuções, ativar a capacidade de reprodução de ciência de dados e auditability.

Pode também implementar estes modelos para as suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree).

+ Deve ser instalado o Visual Studio Code. Visual Studio Code é um editor de código fonte simples, poderoso, mas que é executado no seu ambiente de trabalho. Ele vem com suporte incorporado para Python e outras linguagens de programação. Se ainda não instalou o Visual Studio Code [Descubra como](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Instalar o Python 3.5 ou posterior](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Instalar a extensão do Azure Machine Learning para Visual Studio Code

Quando instalar a extensão do Azure Machine Learning, extensões mais duas são instaladas automaticamente (se tiver acesso à internet). Eles são os [extensão da conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) e o [extensão Python do Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Para trabalhar com o Azure Machine Learning, terá de transformar o código do Visual Studio num ambiente de desenvolvimento de Python integrado (IDE). Tem de utilizar a extensão do Python do Microsoft [Python no Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Esta extensão é instalada automaticamente com a extensão do Azure Machine Learning. A extensão faz um excelente IDE com Visual Studio Code, e funciona em qualquer sistema operativo com uma variedade de interprety de Python. A extensão de Microsoft Python utiliza toda a potência do Visual Studio Code para fornecer o preenchimento automático, IntelliSense, linting, depuração e teste de unidade. A extensão de também lhe permite alternar facilmente entre ambientes do Python, incluindo virtuais e ambientes de conda. Para obter mais informações sobre como editar, executar e depurar o código de Python, consulte a [tutorial do Python hello-world](https://code.visualstudio.com/docs/python/python-tutorial).

Para instalar a extensão do Azure Machine Learning:

1. Abra o Visual Studio Code.

1. Num browser, aceda a [do Azure Machine Learning para a extensão do Visual Studio Code (pré-visualização)](https://aka.ms/vscodetoolsforai).

1. Nessa página da web, selecione **instalar**. 

1. No separador de extensão, selecione **instalar**.

1. É aberto um separador de boas-vindos para a extensão no Visual Studio Code e o símbolo do Azure (descrito em vermelho na captura de ecrã seguinte) é adicionado à barra de atividade.

   ![Ícone do Azure na barra de atividade do Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Na caixa de diálogo, selecione **sessão** e siga as instruções para autenticar com o Azure. 
   
   A extensão da conta do Azure, que foi instalada, juntamente com o Azure Machine Learning para a extensão do Visual Studio Code, ajuda-o a autenticar com a sua conta do Azure. Para obter uma lista de comandos, consulte a página para o [extensão da conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Veja a [IntelliCode extensão para o Visual Studio Code (pré-visualização)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode fornece um conjunto de capacidades de IA auxiliado para IntelliSense em Python, tais como de inferir o autocompletions mais relevantes com base no contexto de código atual.

## <a name="install-the-azure-machine-learning-sdk"></a>Instalar o Azure Machine Learning SDK

1. Certifique-se de que o Python 3.5 ou posterior está instalado e é reconhecida pelo Visual Studio Code. Se instalar a aplicação agora, reinicie o Visual Studio Code e [selecionar um interpretador de Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. Na janela de terminal integrada, especifique o interpretador de Python a utilizar. Ou selecione Enter para utilizar o interpretador de Python padrão.

   ![Selecione o interpretador](./media/vscode-tools-for-ai/python.png)

1. No canto inferior direito da janela, é apresentada uma notificação, que indica que o SDK do Azure Machine Learning está a ser instalado automaticamente. O ambiente de Python recém-criado é local e privadas e tem os pré-requisitos do Visual Studio Code para trabalhar com o serviço Azure Machine Learning.

   ![Instalar o Azure Machine Learning SDK para Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Antes de começar a formação e implementar modelos de machine learning no Visual Studio Code, tem de criar uma [Azure Machine Learning a área de trabalho de serviço](concept-azure-machine-learning-architecture.md#workspace) na cloud. Esta área de trabalho irá conter seus modelos e recursos. 

Para criar uma área de trabalho e adicionar a sua primeira experiência:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. É apresentada a barra lateral do Azure Machine Learning.

   [![Criar uma área de trabalho](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. A subscrição do Azure com o botão direito e selecione **criar área de trabalho**. É apresentada uma lista. Na imagem animados de exemplo, o nome da subscrição é **avaliação gratuita**, e a área de trabalho é **TeamWorkspace**. 

1. Selecione um grupo de recursos da lista ou crie um novo utilizando o assistente na paleta de comandos.

1. No campo, escreva um nome exclusivo e claro para sua nova área de trabalho. A imagem de exemplo, a área de trabalho tem o nome **TeamWorkspace**.

1. Selecione Enter para criar a nova área de trabalho. Ele aparece na árvore, abaixo do nome de subscrição.

1. Com o botão direito a **experimentação** nó e escolha **experimentação criar** no menu de contexto.  Experimentações manter o controle de suas execuções com o Azure Machine Learning.

1. No campo, introduza um nome para a experimentação. As capturas de ecrã de exemplo, a experimentação tem o nome **MNIST**.
 
1. Selecione Enter para criar a nova experimentação. A experimentação aparece na árvore, abaixo do nome de área de trabalho.

1. Na área de trabalho, pode com o botão direito uma experimentação para defini-lo como o **Active Directory** experimentar. O **Active Directory** experimentação é sua experimentação atual. A abrir pasta no Visual Studio Code irá ser ligada a esta experiência na cloud. Esta pasta deve conter os seus scripts de Python locais.

Agora seu métricas-chave serão armazenadas da história de experimentação. Da mesma forma, os modelos que treinar serão automaticamente carregados para o Azure Machine Learning e armazenados juntamente com a experimentação métricas e registos. 

[![Anexar uma pasta no Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Criar e gerir os destinos de computação

Com o Azure Machine Learning para Visual Studio Code, pode preparar os dados, formar modelos e implementá-las tanto localmente como destinos de computação remota.

A extensão suporta vários destinos de computação remoto para o Azure Machine Learning. Para obter mais informações, consulte a lista completa de suportados [destinos de computação do Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Criar destinos de computação do Azure Machine Learning no Visual Studio Code

Para criar um destino de computação:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. É apresentada a barra lateral do Azure Machine Learning.

2. Na vista de árvore, expanda a sua subscrição do Azure e a área de trabalho do serviço Azure Machine Learning. Na imagem de exemplo seguinte, é o nome da subscrição **avaliação gratuita**, e a área de trabalho é **TeamWorkspace**. 

3. Sob o nó de área de trabalho, clique com botão direito a **computação** nó e escolha **criar computação**.

4. Escolha o tipo de destino de computação da lista. 

5. Na paleta de comandos, selecione um tamanho de máquina virtual.

6. Na paleta de comandos, no campo, introduza um nome para o destino de computação. 

7. No ficheiro de configuração JSON que se abre num novo separador, especifique as propriedades avançadas. Pode especificar as propriedades, como uma contagem máxima de nós.

8. Quando terminar de configurar o destino de computação, no canto inferior direito da janela, selecione **submeter**.

Eis um exemplo de como criar uma computação do Azure Machine Learning (AMLCompute):

[![Criar a computação AML no Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>O ficheiro de configuração de execução

A extensão de código do Visual Studio cria automaticamente um destino de computação local e as configurações de execução para os seus ambientes locais e de docker no seu computador local. Pode encontrar os ficheiros de configuração de execução sob o nó de destino de computação associado. 

## <a name="train-and-tune-models"></a>Dar formação e otimizar modelos

Utilize o Azure Machine Learning para Visual Studio Code (pré-visualização) para rapidamente reanalisa o seu código, siga os passos e depurar e utilizar a solução para controle de código de origem. 

Para executar a experimentação localmente ao utilizar o Azure Machine Learning:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. É apresentada a barra lateral do Azure Machine Learning.

1. Na vista de árvore, expanda a sua subscrição do Azure e a área de trabalho do serviço Azure Machine Learning. 

1. Sob o nó de área de trabalho, expanda o **computação** nó e o botão direito do mouse a **executar configuração** da computação que pretende utilizar. 

1. Selecione **execute experimentação**.

1. A partir do Explorador de ficheiros, selecione o script que pretende executar. 

1. Selecione **veja a execução da experimentação** para ver o portal do Azure Machine Learning integrado para monitorizar as execuções e ver os modelos de formação.

Eis um exemplo de como executar uma experiência localmente:

[![Executar uma experiência localmente](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Utilizar computações remotas para experimentações no Visual Studio Code

Para utilizar um destino de computação remota para formação, terá de criar um ficheiro de configuração de execução. Este ficheiro diz ao Azure Machine Learning não só onde executar a sua experimentação, mas também como preparar o ambiente.

#### <a name="the-conda-dependencies-file"></a>O ficheiro de dependências de conda

Por predefinição, é criado um novo ambiente de conda para e suas dependências de instalação são geridas. No entanto, tem de especificar as suas dependências e suas versões no *aml_config/conda_dependencies.yml* ficheiro. 

O fragmento seguinte da predefinição *aml_config/conda_dependencies.yml* especifica `tensorflow=1.12.0`. Se não especificar a versão da dependência, será utilizada a versão mais recente. Pode adicionar dependências adicionais no ficheiro de configuração.

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

Para executar a experimentação com o Azure Machine Learning no remoto destino de computação:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. É apresentada a barra lateral do Azure Machine Learning.

1. Na vista de árvore, expanda a sua subscrição do Azure e a área de trabalho do serviço Azure Machine Learning. 

1. Na janela do editor, o script de Python com o botão direito e selecione **AML: Executar como experimentação no Azure**. 

1. Na paleta de comandos, selecione o destino de computação. 

1. Na paleta de comandos, no campo, introduza o nome de configuração de execução. 

1. Editar a *conda_dependencies.yml* ficheiro para especificar dependências de tempo de execução da experimentação. Em seguida, no canto inferior direito da janela, selecione **submeter**. 

1. Selecione **veja a execução da experimentação** para ver o portal do Azure Machine Learning integrado para monitorizar as execuções e ver os modelos de formação.

Eis um exemplo de como executar uma experiência num destino de computação remota:

[![Executar uma experiência num destino remoto](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Implementar e gerir modelos
No Azure Machine Learning, pode implementar e gerir os seus modelos de machine learning na nuvem e na periferia. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registar o seu modelo para o Azure Machine Learning do Visual Studio Code

Agora que Treinou seu modelo, pode registá-lo na sua área de trabalho. Pode controlar e implementar modelos registados.

Para registar o seu modelo:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. É apresentada a barra lateral do Azure Machine Learning.

1. Na vista de árvore, expanda a sua subscrição do Azure e a área de trabalho do serviço Azure Machine Learning.

1. Sob o nó de área de trabalho, clique com botão direito **modelos** e escolha **modelo registar**.

1. Na paleta de comandos, no campo, introduza um nome de modelo. 

1. Na lista, escolha se pretende carregar um **ficheiro de modelo** (para modelos de únicos) ou uma **pasta modelo** (para modelos com vários ficheiros, como o TensorFlow). 

1. Selecione o ficheiro ou pasta.

1. Quando concluir a configuração de suas propriedades de modelo, no canto inferior direito da janela, selecione **submeter**. 

Eis um exemplo de como registar o seu modelo para o Azure Machine Learning:

[![Registar um modelo de AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Implementar o seu serviço do Visual Studio Code

No Visual Studio Code, pode implementar o serviço da web para:
+ O Azure Container Instances (ACI) para fins de teste.
+ O Azure Kubernetes Service (AKS) para produção.

Não precisa de criar um contentor do ACI para testar com antecedência, porque os contentores ACI são criados em tempo real. No entanto, terá de configurar clusters do AKS com antecedência. Para obter mais informações, consulte [implementar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

Para implementar um serviço web:

1. Na barra de atividade do Visual Studio Code, selecione o ícone do Azure. É apresentada a barra lateral do Azure Machine Learning.

1. Na vista de árvore, expanda a sua subscrição do Azure e a sua área de trabalho do serviço do Azure Machine Learning.

1. Sob o nó de área de trabalho, expanda o **modelos** nó.

1. Com o botão direito do modelo que pretende implementar e escolha **implementar o serviço do modelo registado** no menu de contexto.

1. A paleta de comandos, escolha o destino de computação que pretende implementar. 

1. Na paleta de comandos, no campo, introduza um nome para este serviço.  

1. Na paleta de comandos, selecione a tecla Enter no teclado para procurar e selecione o ficheiro de script.

1. Na paleta de comandos, selecione a tecla Enter no teclado para procurar e selecione o ficheiro de dependências de conda.

1. Quando concluir a configuração de suas propriedades de serviço, no canto inferior direito da janela, selecione **submeter** para implementar. No ficheiro de propriedades do serviço, pode especificar um ficheiro do local docker ou um ficheiro de schema.json.

O serviço web agora é implementado.

Eis um exemplo de como implementar um serviço web:

[![Implementar um serviço web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Utilizar atalhos de teclado

Pode utilizar o teclado para aceder às funcionalidades do Azure Machine Learning no Visual Studio Code. O atalho de teclado mais importante saber é Ctrl + Shift + P, que exibe a paleta de comandos. A partir da paleta de comandos, tem acesso a todas as funcionalidades do Visual Studio Code, incluindo atalhos de teclado para as operações mais comuns.

[![Atalhos de teclado para o Azure Machine Learning para Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Passos Seguintes

* Para obter instruções sobre como preparar-se com o Azure Machine Learning fora do Visual Studio Code, consulte [Tutorial: Utilizar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).
* Para obter instruções sobre como editar, executar e depurar código localmente, consulte a [tutorial do Python hello-world](https://code.visualstudio.com/docs/python/python-tutorial).
