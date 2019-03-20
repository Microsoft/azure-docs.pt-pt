---
title: Preparar e implementar modelos a partir do VS Code
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre o Azure Machine Learning para Visual Studio Code e como começar a machine learning e modelos no serviço Azure Machine Learning com o Visual Studio Code de aprendizagem profunda de treinamento e de implantação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 8d63d5f6281de022e8f7e5c9e0a208866fef3f4f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889561"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Utilizar o Visual Studio Code para preparar e implementar modelos de machine learning

Neste artigo, ficará a saber como utilizar o **Azure Machine Learning para Visual Studio Code** extensão para preparar e implementar modelos com o serviço Azure Machine Learning no Visual Studio Code (VS Code) de aprendizagem profunda e de aprendizagem automática.

O Azure Machine Learning fornece suporte para a execução de experimentações localmente e destinos de computação remota. Para cada fase experimental, pode manter um registo de várias execuções, muitas vezes, o que precisar tentar iterativamente técnicas diferentes, hiperparâmetros e muito mais. Pode utilizar o Azure Machine Learning para controlar métricas personalizadas e experimente execuções, ativar a capacidade de reprodução de ciência de dados e auditability.

E pode implantar esses modelos para suas necessidades de teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

+ Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

+ Tem a [do Azure Machine Learning para o VS Code](how-to-vscode-tools.md) configurar a extensão.

+ Tem a [do Azure Machine Learning SDK para Python instalada](how-to-vscode-tools.md) com o VS Code.

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

7. Especifique quaisquer propriedades avançadas no ficheiro de configuração JSON que se abre num novo separador. Pode especificar as propriedades, como uma contagem máxima de nós....

8. Quando tiver concluído a configurar o destino de computação, clique em **submeter** no canto inferior direito da tela.

Eis um exemplo para a criação de uma computação do Azure Machine Learning (AMLCompute): [![Criar AML computação no VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>O ficheiro de configuração de execução

A extensão do VS Code criará automaticamente um destino de computação local e executar configurações para a sua **local** e **docker** ambientes no seu computador local. Os ficheiros de configuração de execução podem ser encontrados no destino de computação associado.

Este é um trecho do arquivo de configuração de execução local padrão. Por predefinição, `userManagedDependencies: True` para que tem de instalar todas as suas bibliotecas/dependências por conta própria e, em seguida, execuções da experimentação local irão utilizar o seu ambiente de Python padrão como especificado pela extensão do VS código Python.

```yaml
# user_managed_dependencies = True indicates that the environment will be user managed. False indicates that Azure Machine Learning service will manage the user environment.
    userManagedDependencies: True
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

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

  # Currently Azure Machine Learning service only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for Azure Machine Learning service execution, history, and data preparation.

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

## <a name="next-steps"></a>Passos Seguintes

Para um passo a passo de treinamento com o Machine Learning fora do VS Code, leia [Tutorial: Utilizar modelos com o Azure Machine Learning](tutorial-train-models-with-aml.md).

Para um passo a passo de editar, executar e depurar o código localmente, consulte o [Python Hello World Tutorial](https://code.visualstudio.com/docs/python/python-tutorial)
