---
title: Configurar um ambiente de desenvolvimento do Python
titleSuffix: Azure Machine Learning service
description: Saiba como configurar um ambiente de desenvolvimento, quando trabalha com o serviço Azure Machine Learning. Neste artigo, saiba como utilizar ambientes de Conda, criar arquivos de configuração e configurar o Jupyter Notebooks, blocos de notas do Azure, Azure Databricks, IDEs, editores de código e a máquina de Virtual de ciência de dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6a51e57cfac326663d41b545c9f2883a446467d3
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340767"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento do Azure Machine Learning

Neste artigo, irá aprender a configurar um ambiente de desenvolvimento para trabalhar com o serviço Azure Machine Learning. Serviço de Machine Learning é independente de plataforma.

Os únicos requisitos para o seu ambiente de desenvolvimento são Python 3, Anaconda (para ambientes isolados) e um ficheiro de configuração que contém as informações da sua área de trabalho do Azure Machine Learning.

Este artigo enfoca os ambientes e ferramentas que se seguem:

* Blocos de notas do Azure: Um serviço de blocos de notas do Jupyter que está alojado na cloud do Azure. É a maneira mais fácil para começar, porque já está instalado o SDK do Azure Machine Learning.

* [A máquina de Virtual de ciência de dados (DSVM)](#dsvm): Um ambiente de desenvolvimento ou experimentação pré-configurado na cloud do Azure foi concebido para o trabalho de ciência de dados e pode ser implementada para instâncias de VM apenas de CPU ou as instâncias baseadas em GPU. Python 3, Conda, blocos de notas do Jupyter e o SDK do Azure Machine Learning já estão instalados. A VM é fornecido com popular aprendizagem automática e aprendizagem profunda editores, ferramentas e estruturas para o desenvolvimento de soluções de aprendizagem automática. Provavelmente é o ambiente de desenvolvimento mais completo do Machine learning na plataforma do Azure.

* [O bloco de notas do Jupyter](#jupyter): Se já estiver a utilizar o bloco de notas do Jupyter, o SDK tem alguns sobrando que deve instalar.

* [Visual Studio Code](#vscode): Se usar o Visual Studio Code, ele tem algumas extensões úteis que podem instalar.

* [O Azure Databricks](#aml-databricks): Uma plataforma de análise de dados populares baseada no Apache Spark. Saiba como obter o SDK do Azure Machine Learning no seu cluster, de modo a que possa implementar modelos.

Se já tiver um ambiente de Python 3 ou deseja apenas os passos básicos para instalar o SDK, consulte a [computador Local](#local) secção.

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho de serviço do Azure Machine Learning. Para criar a área de trabalho, consulte [introdução ao serviço Azure Machine Learning](quickstart-get-started.md).

- Ambos os [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html) Gestor de pacotes.

    > [!IMPORTANT]
    > Anaconda e Miniconda não são necessários quando estiver a utilizar blocos de notas do Azure.

- No Linux ou macOS, tem do shell de bash.

    > [!TIP]
    > Se estiver no Linux ou macOS e utiliza uma shell que não seja o bash (por exemplo, zsh) poderá receber erros ao executar alguns comandos. Para contornar este problema, utilize o `bash` comando para iniciar um novo shell do bash e execute os comandos lá.

- No Windows, terá da linha de comandos ou prompt Anaconda (por Anaconda e Miniconda instalado).

## <a id="aznotebooks"></a>Blocos de notas do Azure

[Blocos de notas do Azure](https://notebooks.azure.com) (pré-visualização) é um ambiente de desenvolvimento interativo na cloud do Azure. É a maneira mais fácil para começar a utilizar com o desenvolvimento do Azure Machine Learning.

* Já está instalado o SDK do Azure Machine Learning.
* Depois de criar uma área de trabalho do serviço do Azure Machine Learning no portal do Azure, pode clicar num botão para configurar automaticamente o ambiente de bloco de notas do Azure para trabalhar com a área de trabalho.

Para começar a desenvolver com blocos de notas do Azure, veja [introdução ao serviço Azure Machine Learning](quickstart-get-started.md).

Por predefinição, os blocos de notas do Azure utiliza uma camada de serviço gratuito que está limitada a 4GB de memória e 1GB de dados. Pode, no entanto, remover estes limites ao anexar uma instância de máquina de Virtual de ciência de dados para o projeto de blocos de notas do Azure. Para obter mais informações, consulte [gerir e configurar projetos de blocos de notas do Azure - camada de computação](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="dsvm"></a>Máquina de Virtual de ciência de dados

A DSVM é uma imagem personalizada de máquina virtual (VM). Foi concebido para o trabalho de ciência de dados que está pré-configurada com:

  - Como o TensorFlow, PyTorch, Scikit-saiba, XGBoost e o Azure Machine Learning SDK pacotes
  - Ferramentas de ciência de dados populares, tais como Spark autónomo e exploração de
  - Ferramentas do Azure, como a CLI do Azure, o AzCopy e o Explorador de armazenamento
  - Ambientes de desenvolvimento integrado (IDEs), como o Visual Studio Code e PyCharm
  - Servidor de bloco de notas do Jupyter

O SDK do Azure Machine Learning funciona na versão o Ubuntu ou o Windows da DSVM. Mas se planeja usar a DSVM como destino de computação, Ubuntu apenas é suportada.

Para utilizar a DSVM como um ambiente de desenvolvimento, faça o seguinte:

1. Crie uma DSVM em qualquer um dos seguintes ambientes:

    * O portal do Azure:

        * [Criar uma máquina de Virtual de ciência de dados do Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Criar uma máquina de Virtual de ciência de dados do Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * A CLI do Azure:

        > [!IMPORTANT]
        > * Ao utilizar a CLI do Azure, tem primeiro de iniciar sessão sua subscrição do Azure utilizando o `az login` comando.
        >
        > * Ao utilizar os comandos neste passo, tem de fornecer um nome de grupo de recursos, um nome para a VM, um nome de utilizador e uma palavra-passe.

        * Para criar uma máquina de Virtual de ciência de dados do Ubuntu, utilize o seguinte comando:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Para criar uma máquina de Virtual de ciência de dados do Windows, utilize o seguinte comando:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. O SDK do Azure Machine Learning já está instalado no DSVM. Para utilizar o ambiente de Conda que contém o SDK, utilize um dos seguintes comandos:

    * Para DSVM em Ubuntu:

        ```shell
        conda activate py36
        ```

    * Para Windows DSVM:

        ```shell
        conda activate AzureML
        ```

1. Para verificar se pode acessar o SDK e verificar a versão, utilize o seguinte código de Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Para configurar a DSVM para utilizar a sua área de trabalho do serviço do Azure Machine Learning, consulte a [criar um ficheiro de configuração da área de trabalho](#workspace) secção.

Para obter mais informações, consulte [máquinas de virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Computador local

Quando estiver a utilizar um computador local (que também pode ser uma máquina virtual remota), criar um ambiente de Anaconda e instale o SDK, fazendo o seguinte:

1. Transfira e instale [Anaconda](https://www.anaconda.com/distribution/#download-section) (versão Python 3.7) se ainda não o tiver.

1. Abra uma linha de Anaconda e criar um ambiente com os seguintes comandos:

    Execute o seguinte comando para criar o ambiente.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Em seguida, ative o ambiente.

    ```shell
    conda activate myenv
    ```

    Este exemplo cria um ambiente com o python 3.6.5, mas qualquer subversions específicos podem ser escolhidos. Não é possível garantir compatibilidade SDK com determinadas versões principais (3.5 + é recomendado), e é recomendado para experimentar um versão/subversion diferente no seu ambiente de Anaconda caso se depare com erros. Irá demorar alguns minutos a criar o ambiente, enquanto os componentes e os pacotes são transferidos.

1. Execute os seguintes comandos no seu ambiente novo para ativar kernels ipython específicos do ambiente. Isto irá garantir que o esperado de kernel e o pacote de importar o comportamento ao trabalhar com blocos de notas do Jupyter nos ambientes de Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Em seguida, execute o seguinte comando para criar o kernel:

    ```shell
    ipython kernel install --user
    ```

1. Utilize os seguintes comandos para instalar pacotes:

    Este comando instala base SDK do Azure Machine Learning com o bloco de notas e automl extras. O `automl` extra é uma instalação grande e pode ser removido dos colchetes, se não pretender executar experiências de automatizada de machine learning. O `automl` extra também inclui o SDK do Azure Machine Learning Data Prep por predefinição como uma dependência.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Utilize este comando para instalar o SDK do Azure Machine Learning Data Prep por conta própria:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Se obtiver uma mensagem que não pode ser desinstalado PyYAML, utilize o seguinte comando em vez disso:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Irá demorar alguns minutos para instalar o SDK. Consulte a [instalar guia](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para obter mais informações sobre as opções de instalação.

1. Instale outros pacotes para o experimentação da aprendizagem automática.

    Utilizar qualquer um dos seguintes comandos e substitua  *\<novo pacote >* com o pacote que pretende instalar. Instalar pacotes através de `conda install` requer que o pacote faz parte dos canais atuais (novos canais podem ser adicionados na Anaconda Cloud).

    ```shell
    conda install <new package>
    ```

    Em alternativa, pode instalar pacotes através de `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Blocos de notas do Jupyter

Blocos de notas do Jupyter fazem parte do [Jupyter projeto](https://jupyter.org/). Eles fornecem uma experiência interativa de codificação onde criar documentos que misturam código em direto com texto narrativo e gráficos. Blocos de notas do Jupyter também são uma ótima maneira de compartilhar seus resultados com outras pessoas, uma vez que pode salvar a saída de suas seções de código no documento. Pode instalar o Jupyter Notebooks numa variedade de plataformas.

O procedimento a [computador Local](#local) secção instala os componentes necessários para a execução de blocos de notas do Jupyter num ambiente de Anaconda. Para ativar a esses componentes no seu ambiente de bloco de notas do Jupyter, faça o seguinte:

1. Abra uma linha de Anaconda e ativar o seu ambiente.

    ```shell
    conda activate myenv
    ```

1. Inicie o servidor de bloco de notas do Jupyter com o seguinte comando:

    ```shell
    jupyter notebook
    ```

1. Para verificar que o bloco de notas do Jupyter pode utilizar o SDK, crie uma **New** bloco de notas, selecione **Python 3** como sua kernel e, em seguida, execute o seguinte comando numa célula de bloco de notas:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se encontrar problemas de importação de módulos e receber um `ModuleNotFoundError`, certifique-se de que o kernel de Jupyter está ligado para o caminho correto para o seu ambiente, executando o seguinte código numa célula de bloco de notas.

    ```python
    import sys
    sys.path
    ```

1. Para configurar o bloco de notas do Jupyter para utilizar a sua área de trabalho do serviço do Azure Machine Learning, vá para o [criar um ficheiro de configuração da área de trabalho](#workspace) secção.

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code é um editor de código entre plataformas. Ele se baseia numa instalação de Python 3 e Conda local para o suporte de Python, mas ele fornece ferramentas adicionais para trabalhar com IA. Ele também fornece suporte para selecionar o ambiente de Conda a partir do editor de código.

Para utilizar o Visual Studio Code para desenvolvimento, faça o seguinte:

1. Para saber como utilizar o Visual Studio Code para o desenvolvimento de Python, veja [introdução ao Python no VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Para selecionar o ambiente de Conda, abra o VS Code e, em seguida, selecione Ctrl + Shift + P (Linux e Windows) ou comando + Shift + P (Mac).
    O __palete de comando__ abre.

1. Introduza __Python: Selecione o interpretador__e, em seguida, selecione o ambiente de Conda.

1. Para validar que pode utilizar o SDK, criar e, em seguida, execute um novo ficheiro de Python (. PY) que contém o seguinte código:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Para instalar a extensão do Azure Machine Learning para Visual Studio Code, veja [Tools para IA](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para obter mais informações, consulte [utilização do Azure Machine Learning para Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
O Azure Databricks é um ambiente baseado em Apache Spark na cloud do Azure. Ele fornece um ambiente de bloco de notas com base em colaboração com o cluster de computação com base da CPU ou de GPU.

Como o Azure Databricks funciona com o serviço Azure Machine Learning:
+ Pode preparar um modelo com o Spark MLlib e implementar o modelo para o ACI/AKS a partir do Azure Databricks. 
+ Também pode utilizar [automatizada aprendizagem](concept-automated-ml.md) recursos num SDK de ML especiais do Azure com o Azure Databricks.
+ Pode utilizar o Azure Databricks como um destino de computação de um [pipeline do Azure Machine Learning](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Configurar o seu cluster do Databricks

Criar uma [cluster do Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Algumas definições aplicam-se apenas se instalar o SDK para aprendizagem automatizada no Databricks.
**Irá demorar alguns minutos para criar o cluster.**

Utilize estas definições:

| Definição |Aplica-se a| Value |
|----|---|---|
| Nome do cluster |sempre| yourclustername |
| Runtime do Databricks |sempre| Qualquer tempo de execução não ML (ML não 4.x, 5.x) |
| Versão de Python |sempre| 3 |
| Trabalhos |sempre| 2 ou superior |
| Tipos de VM de nós de trabalho <br>(determina o número máximo de iterações simultâneas) |ML automatizada<br>apenas| Com otimização de memória VM preferida |
| Ativar o Dimensionamento Automático |ML automatizada<br>apenas| Desmarque a opção |

Aguarde até que o cluster está em execução antes de prosseguir.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instalar o SDK correto para uma biblioteca do Databricks
Quando o cluster estiver em execução, [criar uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote do SDK do Azure Machine Learning adequado ao seu cluster. 

1. Escolher **apenas uma** opção (outra instalação do SDK não são suportadas)

   |SDK&nbsp;pacote&nbsp;extras|Origem|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Para o Databricks| Carregar Python ovo ou PyPI | azureml-sdk[databricks]|
   |Para o Databricks - com-<br> funcionalidades de ML automatizadas| Carregar Python ovo ou PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Não existem outros extras SDK podem ser instalados. Escolha apenas uma das opções anteriores [databricks] ou [automl_databricks].

   * Não selecione **anexar automaticamente a todos os clusters**.
   * Selecione **Attach** junto ao seu nome de cluster.

1. Monitor de erros até que o estado é alterado para **ligado**, que pode demorar vários minutos.  Se falhar neste passo, verifique o seguinte: 

   Tente reiniciar o seu cluster por:
   1. No painel esquerdo, selecione **Clusters**.
   1. Na tabela, selecione o nome do cluster.
   1. Sobre o **bibliotecas** separador, selecione **reiniciar**.
      
   Considere também:
   + Alguns pacotes e, por exemplo, `psutil`, pode causar conflitos de Databricks durante a instalação. Para evitar esses erros, instalar pacotes pela versão de lib congelando, tais como `pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0`. 
   + Em alternativa, se tiver uma versão antiga do SDK, desmarcá-la a partir de bibliotecas de instalados do cluster e mover para o lixo. Instalar a nova versão do SDK e reinicie o cluster. Se houver um problema depois disso, desligar e voltar a anexar o cluster.

Se a instalação foi concluída com êxito, a biblioteca importada deverá ser semelhante um dos seguintes:
   
SDK para o Databricks **_sem_** automatizada aprendizagem ![Azure Machine Learning SDK para o Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK para o Databricks **WITH** automatizada aprendizagem ![automatizada de SDK com aprendizagem instalada no Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Começar a explorar

Experimente:
+ Transfira o [ficheiro de arquivo do bloco de notas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) do SDK do Azure Databricks/Azure Machine Learning e [importar o ficheiro de arquivo](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) no seu cluster do Databricks.  
  Embora muitos blocos de notas de exemplo estão disponíveis, **apenas [estes blocos de notas do exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) profissional com o Azure Databricks.**
  
+ Saiba como [criar um pipeline com o Databricks como a computação de treinamento](how-to-create-your-first-pipeline.md).

## <a id="workspace"></a>Criar um ficheiro de configuração da área de trabalho

O ficheiro de configuração da área de trabalho é um ficheiro JSON que informa o SDK como se comunicar com a sua área de trabalho do serviço do Azure Machine Learning. O ficheiro é denominado *config*, e ele tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Este ficheiro JSON tem de ser a estrutura do diretório que contém os scripts de Python ou blocos de notas do Jupyter. Pode ter o mesmo diretório, um subdiretório nomeado *aml_config*, ou num diretório principal.

Para utilizar este ficheiro a partir do código, utilize `ws=Workspace.from_config()`. Esse código carrega as informações do arquivo e liga-se a sua área de trabalho.

Pode criar o ficheiro de configuração de três formas:

* **Siga os [guia de início rápido do Azure Machine Learning](quickstart-get-started.md)**: R *config* ficheiro é criado na sua biblioteca de blocos de notas do Azure. O ficheiro contém as informações de configuração para a área de trabalho. Pode transferir ou copiar o *config* para outros ambientes de desenvolvimento.

* **Criar o ficheiro manualmente**: Com este método, utilize um editor de texto. Pode encontrar os valores que ir para o ficheiro de configuração ao visitar a sua área de trabalho do [portal do Azure](https://portal.azure.com). Copie o nome de área de trabalho, grupo de recursos e os valores de ID de subscrição e utilizá-los no ficheiro de configuração.

     ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* **Criar o ficheiro através de programação**: O fragmento de código seguinte, vai ligar a uma área de trabalho, fornecendo o ID de subscrição, o grupo de recursos e o nome de área de trabalho. Em seguida, guardar a configuração de área de trabalho para o ficheiro:

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

    Esse código grava o ficheiro de configuração para o *aml_config/config.json* ficheiro.

## <a name="next-steps"></a>Passos Seguintes

- [Preparar um modelo](tutorial-train-models-with-aml.md) no Azure Machine Learning com o conjunto de dados MNIST
- Ver os [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) referência
- Saiba mais sobre o [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)
