---
title: Criar ambiente de desenvolvimento python
titleSuffix: Azure Machine Learning
description: Crie ambientes de desenvolvimento de Python de aprendizagem de máquinas Azure em cadernos Jupyter, Código de Estúdio Visual, Azure Databricks e Máquinas Virtuais de Ciência de Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1, devx-track-azurecli
ms.openlocfilehash: 03d10f71b585090157eff164cc98246f50608fe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018788"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Crie um ambiente de desenvolvimento python para a aprendizagem de máquinas Azure

Aprenda a configurar um ambiente de desenvolvimento python para a aprendizagem de máquinas Azure.

A tabela a seguir mostra cada ambiente de desenvolvimento abrangido por este artigo, juntamente com prós e contras.

| Ambiente | Vantagens | Desvantagens |
| --- | --- | --- |
| [Ambiente local](#local) | Controlo total do seu ambiente de desenvolvimento e dependências. Corra com qualquer ferramenta de construção, ambiente ou IDE à sua escolha. | Demora mais tempo a começar. Devem ser instalados pacotes SDK necessários e um ambiente também deve ser instalado se ainda não tiver um. |
| [A Máquina Virtual da Ciência de Dados (DSVM)](#dsvm) | Semelhante à instância computacional baseada na nuvem (Python e o SDK estão pré-instalados), mas com mais ferramentas populares de ciência de dados e machine learning pré-instaladas. Fácil de escalar e combinar com outras ferramentas personalizadas e fluxos de trabalho. | Uma experiência de início mais lenta em comparação com a instância computacional baseada na nuvem. |
| [Instância de computação do Azure Machine Learning](#compute-instance) | A maneira mais fácil de começar. Todo o SDK já está instalado no seu espaço de trabalho VM, e os tutoriais de cadernos estão pré-clonados e prontos para funcionar. | Falta de controlo sobre o seu ambiente de desenvolvimento e dependências. Custo adicional incorrido para o Linux VM (VM pode ser interrompido quando não está a ser utilizado para evitar encargos). Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | Ideal para executar fluxos de trabalho intensivos de aprendizagem de máquinas em larga escala na plataforma escalável Apache Spark. | Exagero para aprendizagem automática experimental, ou experiências e fluxos de trabalho em menor escala. Custo adicional incorrido para a Azure Databricks. Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/databricks/) |

Este artigo também fornece dicas de utilização adicionais para as seguintes ferramentas:

* Cadernos Jupyter: Se já está a usar cadernos Jupyter, o SDK tem alguns extras que deve instalar.

* Código do Estúdio Visual: Se utilizar o Código do Estúdio Visual, a [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) inclui um extenso suporte linguístico para Python, bem como funcionalidades para tornar o trabalho com a Azure Machine Learning muito mais conveniente e produtivo.

## <a name="prerequisites"></a>Pré-requisitos

* Espaço de trabalho de aprendizagem automática Azure. Se não tiver um, pode criar um espaço de trabalho de aprendizagem automática Azure através dos modelos [Azure](how-to-manage-workspace.md) [CLI](how-to-manage-workspace-cli.md#create-a-workspace)e [Azure Resource Manager](how-to-create-workspace-template.md).

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> Apenas local e DSVM: Criar um ficheiro de configuração do espaço de trabalho

O ficheiro de configuração do espaço de trabalho é um ficheiro JSON que diz ao SDK como comunicar com o seu espaço de trabalho Azure Machine Learning. O ficheiro é nomeado *config.jsem*, e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Este ficheiro JSON deve estar na estrutura do diretório que contém os seus scripts Python ou Cadernos Jupyter. Pode estar no mesmo diretório, num subdiretório chamado *.azureml,* ou num diretório-mãe.

Para utilizar este ficheiro a partir do seu código, utilize o [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) método. Este código carrega as informações do ficheiro e liga-se ao seu espaço de trabalho.

Criar um ficheiro de configuração do espaço de trabalho num dos seguintes métodos:

* Portal do Azure

    **Descarregue o ficheiro**: No [portal Azure,](https://ms.portal.azure.com)selecione  **Descarregue config.jsna** secção **'Visão Geral'** do seu espaço de trabalho.

    ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* Azure Máquina aprendendo Python SDK

    Crie um script para ligar ao seu espaço de trabalho Azure Machine Learning e use o método para gerar o [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) seu ficheiro e guardá-lo como *.azureml/config.jsligado .* Certifique-se de `subscription_id` `resource_group` substituir, e `workspace_name` por seu próprio.

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

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>Computador local ou ambiente VM remoto

Pode configurar um ambiente num computador local ou numa máquina virtual remota, como uma instância de cálculo de aprendizagem automática Azure ou VM da Data Science. 

Para configurar um ambiente de desenvolvimento local ou VM remoto:

1. Crie um ambiente virtual Python (virtualenv, conda).

    > [!NOTE]
    > Apesar de não ser necessário, recomenda-se que utilize [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://www.anaconda.com/download/) para gerir ambientes virtuais python e instalar pacotes.

    > [!IMPORTANT]
    > Se estiver no Linux ou no macOS e utilizar uma concha que não seja a bash (por exemplo, zsh) poderá receber erros quando executar alguns comandos. Para contornar este problema, use o `bash` comando para iniciar uma nova carapaça e executar os comandos lá.

1. Ative o seu novo ambiente virtual Python.
1. Instale o [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
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

O Data Science VM é uma imagem personalizada da máquina virtual (VM) que pode usar como ambiente de desenvolvimento. É projetado para trabalhos de ciência de dados que são ferramentas pré-configuradas e software como:

  - Pacotes como TensorFlow, PyTorch, Scikit-learn, XGBoost e a Azure Machine Learning SDK
  - Ferramentas populares de ciência de dados, como Spark Standalone e Drill
  - Ferramentas azure como o Azure CLI, AzCopy e Storage Explorer
  - Ambientes de desenvolvimento integrados (IDEs) como Visual Studio Code e PyCharm
  - Servidor de cadernos Jupyter

Para obter uma lista mais completa das ferramentas, consulte o guia de [ferramentas VM da Data Science](data-science-virtual-machine/tools-included.md).

> [!IMPORTANT]
> Se planeia usar o VM da Data Science como [alvo de computação](concept-compute-target.md) para os seus trabalhos de formação ou inferencionar, apenas ubuntu é apoiado.

Para utilizar o VM da Ciência dos Dados como um ambiente de desenvolvimento:

1. Criar um VM de Ciência de Dados utilizando um dos seguintes métodos:

    * Utilize o portal Azure para criar um [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) ou Um DSVM [do Windows.](data-science-virtual-machine/provision-vm.md)
    * [Crie um VM de Ciência de Dados utilizando modelos ARM](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Utilizar a CLI do Azure

        Para criar um Ubuntu Data Science VM, utilize o seguinte comando:

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
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

    * Para Ubuntu Data Science VM:

        ```bash
        conda activate py36
        ```

    * Para o Windows Data Science VM:

        ```bash
        conda activate AzureML
        ```

1. Para configurar o Data Science VM para utilizar o seu espaço de trabalho Azure Machine Learning, crie um ficheiro de [configuração do espaço de trabalho](#workspace) ou utilize um existente.

Semelhante aos ambientes locais, você pode usar o Código de Estúdio Visual e a extensão do [Código de Estúdio Visual Azure Machine Learning](#vscode) para interagir com a Azure Machine Learning.

Para mais informações, consulte [as Máquinas Virtuais da Ciência dos Dados.](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)


## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo](tutorial-train-models-with-aml.md) em Azure Machine Learning com o conjunto de dados MNIST.
- Consulte o [Azure Machine Learning SDK para referência Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). 