---
title: Criar ambiente de desenvolvimento ! Pitão
titleSuffix: Azure Machine Learning
description: Aprenda a criar um ambiente de desenvolvimento Python para a Azure Machine Learning. Use ambientes Conda, crie ficheiros de configuração e configuure o seu próprio servidor de cadernos baseado na nuvem, Cadernos Jupyter, Azure Databricks, IDEs, editores de código e a Máquina Virtual de Ciência de Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: ee1753932018d0ea3a627ff2a6c7b85dc0a65de1
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674851"
---
# <a name="set-up-a-development-environment-for-azure-machine-learning"></a>Criar um ambiente de desenvolvimento para a Azure Machine Learning

Aprenda a configurar um ambiente de desenvolvimento python para a aprendizagem de máquinas Azure.

A tabela a seguir mostra cada ambiente de desenvolvimento abrangido por este artigo, juntamente com prós e contras.

| Ambiente | Vantagens | Desvantagens |
| --- | --- | --- |
| [Ambiente local](#local) | Controlo total do seu ambiente de desenvolvimento e dependências. Corra com qualquer ferramenta de construção, ambiente ou IDE à sua escolha. | Demora mais tempo a começar. Devem ser instalados pacotes SDK necessários e um ambiente também deve ser instalado se ainda não tiver um. |
| [Instância de computação do Azure Machine Learning](#compute-instance) | A maneira mais fácil de começar. Todo o SDK já está instalado no seu espaço de trabalho VM, e os tutoriais de cadernos estão pré-clonados e prontos para funcionar. | Falta de controlo sobre o seu ambiente de desenvolvimento e dependências. Custo adicional incorrido para o Linux VM (VM pode ser interrompido quando não está a ser utilizado para evitar encargos). Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) |
| [Azure Databricks](#aml-databricks) | Ideal para executar fluxos de trabalho intensivos de aprendizagem de máquinas em larga escala na plataforma escalável Apache Spark. | Exagero para aprendizagem automática experimental, ou experiências e fluxos de trabalho em menor escala. Custo adicional incorrido para a Azure Databricks. Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/databricks/) |
| [A Máquina Virtual da Ciência de Dados (DSVM)](#dsvm) | Semelhante à instância computacional baseada na nuvem (Python e o SDK estão pré-instalados), mas com mais ferramentas populares de ciência de dados e machine learning pré-instaladas. Fácil de escalar e combinar com outras ferramentas personalizadas e fluxos de trabalho. | Uma experiência de início mais lenta em comparação com a instância computacional baseada na nuvem. |

Este artigo também fornece dicas de utilização adicionais para as seguintes ferramentas:

* Cadernos Jupyter: Se já está a usar cadernos Jupyter, o SDK tem alguns extras que deve instalar.

* Código do Estúdio Visual: Se utilizar o Código do Estúdio Visual, a [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) inclui um extenso suporte linguístico para Python, bem como funcionalidades para tornar o trabalho com a Azure Machine Learning muito mais conveniente e produtivo.

## <a name="prerequisites"></a>Pré-requisitos

* Espaço de trabalho de aprendizagem automática Azure. Se não tiver um, pode criar um espaço de trabalho de aprendizagem automática Azure através dos modelos [Azure](how-to-manage-workspace.md) [CLI](how-to-manage-workspace-cli.md#create-a-workspace)e [Azure Resource Manager](how-to-create-workspace-template.md).

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> (Apenas local e DSVM) Criar um ficheiro de configuração do espaço de trabalho

O ficheiro de configuração do espaço de trabalho é um ficheiro JSON que diz ao SDK como comunicar com o seu espaço de trabalho Azure Machine Learning. O ficheiro é nomeado *config.jsem* , e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Este ficheiro JSON deve estar na estrutura do diretório que contém os seus scripts Python ou Cadernos Jupyter. Pode estar no mesmo diretório, num subdiretório chamado *.azureml,* ou num diretório-mãe.

Para utilizar este ficheiro a partir do seu código, utilize o [`Workspace.from_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) método. Este código carrega as informações do ficheiro e liga-se ao seu espaço de trabalho.

Criar um ficheiro de configuração do espaço de trabalho num dos seguintes métodos:

* Portal do Azure

    **Descarregue o ficheiro** : No [portal Azure,](https://ms.portal.azure.com)selecione  **Descarregue config.jsna** secção **'Visão Geral'** do seu espaço de trabalho.

    ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* Azure Máquina aprendendo Python SDK

    Crie um script para ligar ao seu espaço de trabalho Azure Machine Learning e use o método para gerar o [`write_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) seu ficheiro e guardá-lo como *.azureml/config.jsligado .* Certifique-se de `subscription_id` `resource_group` substituir, e `workspace_name` por seu próprio.

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

## <a name="local-computer"></a><a id="local"></a>Computador local

Para configurar um ambiente de desenvolvimento local (que também pode ser uma máquina virtual remota, como um caso de cálculo de aprendizagem automática Azure ou DSVM):

1. Crie um ambiente virtual Python (virtualenv, conda).

    > [!NOTE]
    > Apesar de não ser necessário, recomenda-se que utilize [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://www.anaconda.com/download/) para gerir ambientes virtuais python e instalar pacotes.

    > [!IMPORTANT]
    > Se estiver no Linux ou no macOS e utilizar uma concha que não seja a bash (por exemplo, zsh) poderá receber erros quando executar alguns comandos. Para contornar este problema, use o `bash` comando para iniciar uma nova carapaça e executar os comandos lá.

1. Ative o seu novo ambiente virtual Python.
1. Instale o [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
1. Para configurar o seu ambiente local para utilizar o seu espaço de trabalho Azure Machine Learning, crie um ficheiro de [configuração de espaço de trabalho](#workspace) ou utilize um existente.

Agora que tens o teu ambiente local preparado, estás pronto para começar a trabalhar com o Azure Machine Learning. Veja o [Azure Machine Learning Python a começar o guia](tutorial-1st-experiment-sdk-setup-local.md) para começar.

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

Ao executar um servidor local do Jupyter Notebook, recomenda-se que crie um núcleo IPython para o seu ambiente virtual Python. Isto ajuda a garantir o comportamento esperado de importação de núcleo e pacote.

1. Ativar núcleos IPython específicos do ambiente

    ```bash
    conda install notebook ipykernel
    ```

1. Crie um núcleo para o seu ambiente virtual Python. Certifique-se de que substitui `<myenv>` pelo nome do seu ambiente virtual Python.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Lançar o servidor Jupyter Notebook

Consulte o [repositório de cadernos Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks) para começar com Azure Machine Learning e Jupyter Notebooks.

> [!NOTE]
> Um repositório de exemplos orientado pela comunidade pode ser encontrado em https://github.com/Azure/azureml-examples .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Para utilizar o Código do Estúdio Visual para o desenvolvimento:

1. Instale [o Código do Estúdio Visual](https://code.visualstudio.com/Download).
1. Instale a extensão do [Código do Estúdio Visual Azure Machine Learning](tutorial-setup-vscode-extension.md) (pré-visualização).

Assim que tiver a extensão visual Studio Code instalada, pode gerir os seus [recursos de Aprendizagem de Máquinas Azure,](how-to-manage-resources-vscode.md) [executar e depurar experiências](how-to-debug-visual-studio-code.md)e implementar [modelos treinados.](tutorial-train-deploy-image-classification-model-vscode.md)

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Instância de computação do Azure Machine Learning

O caso de [aprendizagem](concept-compute-instance.md) automática Azure é uma estação de trabalho segura e baseada na nuvem Azure que fornece aos cientistas de dados um servidor Jupyter Notebook, JupyterLab, e um ambiente de aprendizagem automática totalmente gerido.

Não há nada para instalar ou configurar para um caso de computação.  

Crie um a qualquer momento a partir do seu espaço de trabalho Azure Machine Learning. Forneça apenas um nome e especifique um tipo Azure VM. Experimente agora com este [Tutorial: Ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md)

Para saber mais sobre casos de computação, incluindo como instalar pacotes, consulte [Criar e gerir um caso de cálculo de Aprendizagem automática Azure](how-to-create-manage-compute-instance.md).

> [!TIP]
> Para evitar que as acusações incorram por uma instância de computação não sustentável, [pare a instância de computação](how-to-create-manage-compute-instance.md#manage).

Além de um servidor Jupyter Notebook e JupyterLab, você pode usar instâncias computatadas na [funcionalidade de caderno integrado dentro do estúdio Azure Machine Learning](how-to-run-jupyter-notebooks.md).

Também pode utilizar a extensão do Código de Estúdio Visual Azure Machine Learning para [configurar uma instância computacional de aprendizagem automática Azure como um servidor remoto do Jupyter Notebook](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Máquina Virtual de Ciência de Dados

O DSVM é uma imagem personalizada da máquina virtual (VM). É projetado para trabalhos de ciência de dados que são ferramentas pré-configuradas e software como:

  - Pacotes como TensorFlow, PyTorch, Scikit-learn, XGBoost e a Azure Machine Learning SDK
  - Ferramentas populares de ciência de dados, como Spark Standalone e Drill
  - Ferramentas azure como o Azure CLI, AzCopy e Storage Explorer
  - Ambientes de desenvolvimento integrados (IDEs) como Visual Studio Code e PyCharm
  - Servidor de cadernos Jupyter

Para obter uma lista mais completa das ferramentas, consulte o guia de [ferramentas incluído no DSVM.](data-science-virtual-machine/tools-included.md)

> [!IMPORTANT]
> Se planeia usar o DSVM como [alvo de computação](concept-compute-target.md) para a sua formação ou inferencionar empregos, apenas ubuntu é apoiado.

Usar o DSVM como um ambiente de desenvolvimento

1. Criar um DSVM utilizando um dos seguintes métodos:

    * Utilize o portal Azure para criar um [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) ou Um DSVM [do Windows.](data-science-virtual-machine/provision-vm.md)
    * [Crie um DSVM utilizando modelos ARM](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Utilizar a CLI do Azure

        Para criar um Ubuntu DSVM, utilize o seguinte comando:

        ```azurecli-interactive
        # create a Ubuntu DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Para criar um DSVM do Windows, utilize o seguinte comando:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Ative o ambiente conda contendo o Azure Machine Learning SDK.

    * Para Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Para o Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. Para configurar o DSVM para utilizar o seu espaço de trabalho Azure Machine Learning, crie um ficheiro de [configuração do espaço de trabalho](#workspace) ou utilize um existente.

Semelhante aos ambientes locais, você pode usar o Código de Estúdio Visual e a extensão do [Código de Estúdio Visual Azure Machine Learning](#vscode) para interagir com a Azure Machine Learning.

Para mais informações, consulte [as Máquinas Virtuais da Ciência dos Dados.](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)

## <a name="azure-databricks"></a><a name="aml-databricks"></a> Azure Databricks

Azure Databricks é um ambiente baseado em Apache Spark na nuvem Azure. Fornece um ambiente colaborativo baseado em Caderno com um cpu ou cluster de computação baseado em GPU.

Como a Azure Databricks trabalha com a Azure Machine Learning:

+ Pode treinar um modelo usando o Spark MLlib e implementar o modelo para ACI/AKS a partir de dentro de Azure Databricks.
+ Também pode utilizar capacidades [automatizadas de aprendizagem automática](concept-automated-ml.md) de máquinas num Azure ML SDK com Azure Databricks.
+ Pode utilizar a Azure Databricks como alvo de computação a partir de um [oleoduto de aprendizagem automática Azure](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Configurar o seu cluster Databricks

Criar um [cluster Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Algumas definições só se aplicam se instalar o SDK para aprendizagem automática de máquinas em Databricks.
**Levará alguns minutos para criar o aglomerado.**

Utilize estas definições:

| Definição |Aplica-se a| Valor |
|----|---|---|
| Nome do cluster |sempre| seu nome decluster |
| Runtime do Databricks |sempre|Tempo de execução não-ML 7.1 (scala 2.21, faísca 3.0.0) |
| Versão de Python |sempre| 3 |
| Trabalhadores |sempre| 2 ou mais |
| Tipos de VM de nó de trabalhador <br>(determina max # de iterações simultâneas) |ML Automatizado<br>apenas| VM otimizado de memória preferido |
| Ativar a autoscalagem |ML Automatizado<br>apenas| Desmarcar |

Aguarde até que o aglomerado esteja a funcionar antes de prosseguir.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instale o SDK correto numa biblioteca databricks

Uma vez que o cluster esteja em funcionamento, [crie uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote Azure Machine Learning SDK ao seu cluster. Para um salto automático de ML para o [SDK para databricks com secção automatizada de aprendizagem automática de máquinas.](#sdk-for-databricks-with-automated-machine-learning)

1. Clique com o botão direito na pasta workspace atual onde pretende armazenar a biblioteca. Selecione **Criar**  >  **Biblioteca** .

1. Escolha a seguinte opção (nenhuma outra instalação SDK está suportada)

   |&nbsp;Extras de pacote &nbsp; SDK|Fonte|&nbsp;Nome PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Para databricks| Upload Python Egg ou PyPI | azureml-sdk[databricks]|

   > [!Warning]
   > Não podem ser instalados outros extras SDK. Escolha apenas a `databricks` opção .

   * Não selecione **Fixe automaticamente a todos os clusters** .
   * **Selecione Fixe** ao lado do nome do seu cluster.

1. Monitorize os erros até alterações de estado para **anexação** , o que pode demorar vários minutos.  Se este passo falhar:

   Tente reiniciar o seu cluster:
   1. No painel esquerdo, selecione **Clusters** .
   1. Na tabela, selecione o nome do seu cluster.
   1. No separador **Bibliotecas,** **selecione Restart** .

   Considere também:
   + Em configurar AutoML, ao utilizar a Azure Databricks adicione os seguintes parâmetros:
       1. ```max_concurrent_iterations``` baseia-se no número de nós de trabalhadores no seu agrupamento.
        2. ```spark_context=sc``` baseia-se no contexto de faísca padrão.
   + Ou, se tiver uma versão antiga do SDK, desmarca-a das libs instaladas do cluster e muda-se para o lixo. Instale a nova versão SDK e reinicie o cluster. Se houver algum problema após o reinício, retire-se e recoloque o seu cluster.

Se a instalação tiver sido bem sucedida, a biblioteca importada deve parecer uma destas:

#### <a name="sdk-for-databricks"></a>SDK para databricks
![Azure Machine Learning SDK para databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

#### <a name="sdk-for-databricks-with-automated-machine-learning"></a>SDK para databricks com aprendizagem automática de máquinas
Se o cluster foi criado com databricks non ML runtime 7.1 ou superior, executar o seguinte comando na primeira célula do seu caderno para instalar o AML SDK.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Para databricks non ML runtime 7.0 e inferior, instale o AML SDK utilizando o [script init](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).


### <a name="start-exploring"></a>Começar a explorar

Experimente:
+ Embora muitos cadernos de amostras estejam disponíveis, **[apenas estes cadernos de amostra funcionam](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) com Azure Databricks.**

+ Importe estas amostras diretamente do seu espaço de trabalho. Ver abaixo: ![ Selecione Painel ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ de Importação](./media/how-to-configure-environment/azure-db-import.png)

+ Saiba como [criar um oleoduto com databricks como o computo de treino.](how-to-create-your-first-pipeline.md)

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo](tutorial-train-models-with-aml.md) em Azure Machine Learning com o conjunto de dados MNIST
- Ver o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) referência Python
