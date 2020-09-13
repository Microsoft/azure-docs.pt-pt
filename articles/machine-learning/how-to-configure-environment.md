---
title: Criar um ambiente de desenvolvimento Python
titleSuffix: Azure Machine Learning
description: Aprenda a configurar o seu ambiente de desenvolvimento para a Azure Machine Learning. Use ambientes Conda, crie ficheiros de configuração e configuure o seu próprio servidor de cadernos baseado na nuvem, Cadernos Jupyter, Azure Databricks, IDEs, editores de código e a Máquina Virtual de Ciência de Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8ad22abdf17c68c93a6189db839fe357e7acc91e
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650836"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configure um ambiente de desenvolvimento para a aprendizagem automática Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a configurar um ambiente de desenvolvimento para trabalhar com a Azure Machine Learning. Azure Machine Learning é agnóstico de plataforma. O único requisito difícil para o seu ambiente de desenvolvimento é o Python 3. Recomenda-se também um ambiente isolado como Anaconda ou Virtualenv.

A tabela a seguir mostra cada ambiente de desenvolvimento abrangido por este artigo, juntamente com prós e contras.

| Ambiente | Vantagens | Desvantagens |
| --- | --- | --- |
| [Exemplo de computação de aprendizagem automática de Azure baseada em nuvem](#compute-instance) | A maneira mais fácil de começar. Todo o SDK já está instalado no seu espaço de trabalho VM, e os tutoriais de cadernos estão pré-clonados e prontos para funcionar. | Falta de controlo sobre o seu ambiente de desenvolvimento e dependências. Custo adicional incorrido para o Linux VM (VM pode ser interrompido quando não está a ser utilizado para evitar encargos). Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) |
| [Ambiente local](#local) | Controlo total do seu ambiente de desenvolvimento e dependências. Corra com qualquer ferramenta de construção, ambiente ou IDE à sua escolha. | Demora mais tempo a começar. Devem ser instalados pacotes SDK necessários e um ambiente também deve ser instalado se ainda não tiver um. |
| [Azure Databricks](#aml-databricks) | Ideal para executar fluxos de trabalho intensivos de aprendizagem de máquinas em larga escala na plataforma escalável Apache Spark. | Exagero para aprendizagem automática experimental, ou experiências e fluxos de trabalho em menor escala. Custo adicional incorrido para a Azure Databricks. Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/databricks/) |
| [A Máquina Virtual da Ciência de Dados (DSVM)](#dsvm) | Semelhante à instância computacional baseada na nuvem (Python e o SDK estão pré-instalados), mas com mais ferramentas populares de ciência de dados e machine learning pré-instaladas. Fácil de escalar e combinar com outras ferramentas personalizadas e fluxos de trabalho. | Uma experiência de início mais lenta em comparação com a instância computacional baseada na nuvem. |

Este artigo também fornece dicas de utilização adicionais para as seguintes ferramentas:

* [Cadernos Jupyter](#jupyter): Se já estiver a utilizar o Bloco de Notas Jupyter, o SDK tem alguns extras que deve instalar.

* [Código de Estúdio Visual](#vscode): Se utilizar o Código do Estúdio Visual, a [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) inclui um extenso suporte linguístico para Python, bem como funcionalidades para tornar o trabalho com a Azure Machine Learning muito mais conveniente e produtivo.

## <a name="prerequisites"></a>Pré-requisitos

Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte Criar um espaço de trabalho para [aprendizagem de máquinas Azure.](how-to-manage-workspace.md) Um espaço de trabalho é tudo o que precisa para começar com o seu próprio [servidor de cadernos baseado na nuvem](#compute-instance), um [DSVM](#dsvm)ou [Azure Databricks](#aml-databricks).

Para instalar o ambiente SDK para o seu [computador local,](#local) [o servidor Jupyter Notebook](#jupyter) ou o Código do Estúdio [Visual](#vscode) também precisa:

- Ou o gestor de [pacotes Daaconda](https://www.anaconda.com/download/) ou [Miniconda.](https://conda.io/miniconda.html)

- No Linux ou no macOS, precisas da casca de pancada.

    > [!TIP]
    > Se estiver no Linux ou no macOS e utilizar uma concha que não seja a bash (por exemplo, zsh) poderá receber erros quando executar alguns comandos. Para contornar este problema, use o `bash` comando para iniciar uma nova carapaça e executar os comandos lá.

- No Windows, necessita do pedido de comando ou da solicitação de Anaconda (instalado pela Anaconda e pela Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>O seu próprio caso de computação baseada em nuvem

O caso de [aprendizagem](concept-compute-instance.md) automática Azure é uma estação de trabalho segura e baseada na nuvem Azure que fornece aos cientistas de dados um servidor de cadernos Jupyter, JupyterLab, e um ambiente ML totalmente preparado.

Não há nada para instalar ou configurar para um caso de computação.  Crie um a qualquer momento a partir do seu espaço de trabalho Azure Machine Learning. Forneça apenas um nome e especifique um tipo Azure VM. Experimente agora com este [Tutorial: Ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md)

Para saber mais sobre casos de computação, incluindo como instalar pacotes, consulte [instâncias computacionais](concept-compute-instance.md).

Para parar de incorrer em taxas de cálculo, [pare a instância de cálculo](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Máquina Virtual de Ciência de Dados

O DSVM é uma imagem personalizada da máquina virtual (VM). É projetado para o trabalho de ciência de dados que está pré-configurado com:

  - Pacotes como TensorFlow, PyTorch, Scikit-learn, XGBoost e a Azure Machine Learning SDK
  - Ferramentas populares de ciência de dados, como Spark Standalone e Drill
  - Ferramentas azure como o Azure CLI, AzCopy e Storage Explorer
  - Ambientes de desenvolvimento integrados (IDEs) como Visual Studio Code e PyCharm
  - Servidor de cadernos Jupyter

O Azure Machine Learning SDK funciona na versão Ubuntu ou Windows do DSVM. Mas se planeia usar o DSVM como alvo de computação também, apenas ubuntu é suportado.

Utilizar o DSVM como ambiente de desenvolvimento:

1. Criar um DSVM em qualquer um dos seguintes ambientes:

    * O portal Azure:

        * [Criar uma máquina virtual de ciência de dados Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Criar uma Máquina Virtual de Ciência de Dados do Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * O Azure CLI:

        > [!IMPORTANT]
        > * Quando utilizar o Azure CLI, deve iniciar súmis na sua subscrição Azure utilizando o `az login` comando.
        >
        > * Quando utilizar os comandos neste passo, deve fornecer um nome de grupo de recursos, um nome para o VM, um nome de utilizador e uma palavra-passe.

        * Para criar uma Máquina Virtual Ubuntu Data Science, utilize o seguinte comando:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Para criar uma máquina virtual de ciência de dados do Windows, utilize o seguinte comando:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. O Azure Machine Learning SDK já está instalado no DSVM. Para utilizar o ambiente Conda que contém o SDK, utilize um dos seguintes comandos:

    * Para Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Para o Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. Para verificar se pode aceder ao SDK e verificar a versão, utilize o seguinte código Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Para configurar o DSVM para utilizar o seu espaço de trabalho Azure Machine Learning, consulte a secção [de ficheiros de configuração](#workspace) do espaço de trabalho.

Para mais informações, consulte [as Máquinas Virtuais da Ciência dos Dados.](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)

## <a name="local-computer"></a><a id="local"></a>Computador local

Quando estiver a utilizar um computador local (que também pode ser uma máquina virtual remota), crie um ambiente Anaconda e instale o SDK. Eis um exemplo:

1. Faça o download e instale [Anaconda](https://www.anaconda.com/distribution/#download-section) (versão Python 3.7) se ainda não o tiver.

1. Abra um pedido de Anaconda e crie um ambiente com os seguintes comandos:

    Executar o seguinte comando para criar o ambiente.

    ```bash
    conda create -n myenv python=3.7.7
    ```

    Em seguida, ativar o ambiente.

    ```bash
    conda activate myenv
    ```

    Este exemplo cria um ambiente usando python 3.7.7, mas quaisquer subversões específicas podem ser escolhidas. A compatibilidade da SDK pode não ser garantida com determinadas versões principais (3.5+ é recomendado), e é aconselhável experimentar uma versão/subversão diferente no seu ambiente Anaconda se encontrar erros. Levará vários minutos para criar o ambiente enquanto componentes e pacotes são descarregados.

1. Execute os seguintes comandos no seu novo ambiente para permitir núcleos I Python específicos do ambiente. Isto garantirá o comportamento esperado de importação de núcleo e pacote ao trabalhar com cadernos Jupyter em ambientes Anaconda:

    ```bash
    conda install notebook ipykernel
    ```

    Em seguida, executar o seguinte comando para criar o núcleo:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Utilize os seguintes comandos para instalar pacotes:

    Este comando instala a base Azure Machine Learning SDK com cadernos e `automl` extras. O `automl` extra é uma grande instalação, e pode ser removido dos suportes se não pretender executar experiências automatizadas de aprendizagem automática. O `automl` extra também inclui o Azure Machine Learning Data Prep SDK por padrão como dependência.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Se receber uma mensagem de que o PyYAML não pode ser desinstalado, utilize o seguinte comando:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Começando pelo macOS Catalina, zsh (Z shell) é a concha de login padrão e a concha interativa. Em zsh, use o seguinte comando que escapa aos suportes com \\ " ( backslash):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Levará vários minutos para instalar o SDK. Para obter mais informações sobre as opções de instalação, consulte o [guia de instalação](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).

1. Instale outros pacotes para a sua experimentação de machine learning.

    Utilize qualquer um dos seguintes comandos e *\<new package>* substitua-o pela embalagem que pretende instalar. A instalação de pacotes através `conda install` requer que o pacote faça parte dos canais atuais (novos canais podem ser adicionados na Nuvem de Anaconda).

    ```bash
    conda install <new package>
    ```

    Em alternativa, pode instalar pacotes através de `pip` .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

Os Cadernos Jupyter fazem parte do [Projeto Jupyter.](https://jupyter.org/) Proporcionam uma experiência de codificação interativa onde se criam documentos que misturam código ao vivo com textos narrativos e gráficos. Os Cadernos Jupyter também são uma ótima maneira de partilhar os seus resultados com outros, porque você pode guardar a saída das suas secções de código no documento. Você pode instalar Cadernos Jupyter em várias plataformas.

O procedimento na secção [informática local](#local) instala componentes necessários para a execução de Cadernos Jupyter em ambiente Anaconda.

Para ativar estes componentes no seu ambiente de Caderno Jupyter:

1. Abra um pedido de Anaconda e ative o seu ambiente.

    ```bash
    conda activate myenv
    ```

1. Clone [o repositório gitHub](https://github.com/Azure/MachineLearningNotebooks) para um conjunto de cadernos de amostras.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lance o servidor Jupyter Notebook com o seguinte comando:

    ```bash
    jupyter notebook
    ```

1. Para verificar se o Jupyter Notebook pode usar o SDK, crie um **novo** caderno, selecione **Python 3** como seu núcleo e, em seguida, executar o seguinte comando numa célula de caderno:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se encontrar problemas de importação de módulos e receber um `ModuleNotFoundError` , certifique-se de que o seu núcleo Jupyter está ligado ao caminho correto para o seu ambiente, executando o seguinte código numa célula Notebook.

    ```python
    import sys
    sys.path
    ```

1. Para configurar o Bloco de Notas do Jupyter para utilizar o seu espaço de trabalho Azure Machine Learning, vá à secção [de ficheiros de configuração do espaço de trabalho.](#workspace)

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code é um editor de código de plataforma cruzada muito popular que suporta um vasto conjunto de linguagens e ferramentas de programação através de extensões disponíveis no mercado do [Estúdio Visual.](https://marketplace.visualstudio.com/vscode) A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) instala a [extensão Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para codificação em todos os tipos de ambientes Python (virtual, Anaconda, etc.). Além disso, fornece funcionalidades de conveniência para trabalhar com os recursos de Aprendizagem de Máquinas Azure e executar experiências de Aprendizagem automática Azure tudo sem sair do Código do Estúdio Visual.

Para utilizar o Código do Estúdio Visual para o desenvolvimento:

1. Instale a extensão Azure Machine Learning para Código de Estúdio Visual, ver [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para obter mais informações, consulte [use Azure Machine Learning para Código de Estúdio Visual](tutorial-setup-vscode-extension.md).

1. Aprenda a usar o Código do Estúdio Visual para qualquer tipo de desenvolvimento python, consulte [Começar com Python em VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Para selecionar o ambiente SDK Python que contém o SDK, abra o Código VS e, em seguida, selecione Ctrl+Shift+P (Linux e Windows) ou Command+Shift+P (Mac).
        - A __Paleta de Comando__ abre.

    - Insira __Python: Selecione Intérprete__e, em seguida, selecione o ambiente apropriado

1. Para validar que pode utilizar o SDK, crie um novo ficheiro Python (.py) que contenha o seguinte código:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Executar este código clicando no CodeLens "Run cell" ou simplesmente pressione por turnos.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks é um ambiente baseado em Apache Spark na nuvem Azure. Fornece um ambiente colaborativo baseado em Cadernos com CPU ou cluster compute baseado em GPU.

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
| Runtime do Databricks |sempre|Tempo de execução não-ML 6.5 (scala 2.11, faísca 2.4.3) |
| Versão de Python |sempre| 3 |
| Trabalhadores |sempre| 2 ou mais |
| Tipos de VM de nó de trabalhador <br>(determina max # de iterações simultâneas) |ML Automatizado<br>apenas| VM otimizado de memória preferido |
| Ativar a autoscalagem |ML Automatizado<br>apenas| Desmarcar |

Aguarde até que o aglomerado esteja a funcionar antes de prosseguir.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instale o SDK correto numa biblioteca databricks
Uma vez que o cluster esteja em funcionamento, [crie uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote Azure Machine Learning SDK ao seu cluster.

1. Clique com o botão direito na pasta workspace atual onde pretende armazenar a biblioteca. Selecione **Criar**  >  **Biblioteca**.

1. Escolha **apenas uma** opção (nenhuma outra instalação SDK está suportada)

   |&nbsp;Extras de pacote &nbsp; SDK|Origem|&nbsp;Nome PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Para databricks| Upload Python Egg ou PyPI | azureml-sdk[databricks]|
   |Para databricks -com...<br> capacidades ML automatizadas| Upload Python Egg ou PyPI | `azureml-sdk[automl]`|

   > [!Warning]
   > Não podem ser instalados outros extras SDK. Escolha apenas uma das opções anteriores [ `databricks` ou `automl` [].

   * Não selecione **Fixe automaticamente a todos os clusters**.
   * **Selecione Fixe** ao lado do nome do seu cluster.

1. Monitorize os erros até alterações de estado para **anexação**, o que pode demorar vários minutos.  Se este passo falhar:

   Tente reiniciar o seu cluster:
   1. No painel esquerdo, selecione **Clusters**.
   1. Na tabela, selecione o nome do seu cluster.
   1. No separador **Bibliotecas,** **selecione Restart**.

   Considere também:
   + Em configurar AutoML, ao utilizar a Azure Databricks adicione os seguintes parâmetros:
       1. ```max_concurrent_iterations``` baseia-se no número de nós de trabalhadores no seu agrupamento.
        2. ```spark_context=sc``` baseia-se no contexto de faísca padrão.
   + Ou, se tiver uma versão antiga do SDK, desmarca-a das libs instaladas do cluster e muda-se para o lixo. Instale a nova versão SDK e reinicie o cluster. Se houver algum problema após o reinício, retire-se e recoloque o seu cluster.

Se a instalação tiver sido bem sucedida, a biblioteca importada deve parecer uma destas:

SDK para databricks **_sem_** aprendizagem automática ![ de máquinaS Azure Machine Learning SDK para databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK para databricks **com** machine learning automatizado ![ SDK com aprendizagem automática de máquinas instalada em Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Começar a explorar

Experimente:
+ Embora muitos cadernos de amostras estejam disponíveis, ** [apenas estes cadernos de amostra funcionam](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) com Azure Databricks.**

+ Importe estas amostras diretamente do seu espaço de trabalho. Ver abaixo: ![ Selecione Painel ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ de Importação](./media/how-to-configure-environment/azure-db-import.png)

+ Saiba como [criar um oleoduto com databricks como o computo de treino.](how-to-create-your-first-pipeline.md)

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Criar um ficheiro de configuração do espaço de trabalho

O ficheiro de configuração do espaço de trabalho é um ficheiro JSON que diz ao SDK como comunicar com o seu espaço de trabalho Azure Machine Learning. O ficheiro é nomeado *config.jsem*, e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Este ficheiro JSON deve estar na estrutura do diretório que contém os seus scripts Python ou Cadernos Jupyter. Pode estar no mesmo diretório, num subdiretório chamado *.azureml,* ou num diretório-mãe.

Para utilizar este ficheiro a partir do seu código, utilize `ws=Workspace.from_config()` . Este código carrega as informações do ficheiro e liga-se ao seu espaço de trabalho.

Pode criar o ficheiro de configuração de três formas:

* **Utilize  [ws.write_config:](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)** para escrever um *config.jsno* ficheiro. O ficheiro contém as informações de configuração para o seu espaço de trabalho. Você pode baixar ou copiar o *config.jsem* outros ambientes de desenvolvimento.

* **Descarregue o ficheiro**: No [portal Azure,](https://ms.portal.azure.com)selecione  **Descarregue config.jsna** secção **'Visão Geral'** do seu espaço de trabalho.

     ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* **Crie o ficheiro programáticamente**: No seguinte corte de código, liga-se a um espaço de trabalho fornecendo o ID de subscrição, grupo de recursos e nome do espaço de trabalho. Em seguida, guarda a configuração do espaço de trabalho para o ficheiro:

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

    Este código escreve o ficheiro de configuração para o *ficheiro .azureml/config.js.*

## <a name="next-steps"></a>Próximos passos

- [Treine um modelo](tutorial-train-models-with-aml.md) em Azure Machine Learning com o conjunto de dados MNIST
- Ver o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) referência Python
