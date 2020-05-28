---
title: Criar um ambiente de desenvolvimento Python
titleSuffix: Azure Machine Learning
description: Aprenda a configurar o seu ambiente de desenvolvimento para o Azure Machine Learning. Utilize ambientes Conda, crie ficheiros de configuração e configure o seu próprio servidor de portátil baseado em nuvem, Cadernos Jupyter, Tijolos de Dados Azure, IDEs, editores de código e a Máquina Virtual da Ciência dos Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: ab9a02efedb0e002ac13294429c06e39ea18019c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117473"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configure um ambiente de desenvolvimento para azure machine learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a configurar um ambiente de desenvolvimento para trabalhar com o Azure Machine Learning. Azure Machine Learning é agnóstico da plataforma. O único requisito difícil para o seu ambiente de desenvolvimento é Python 3. Um ambiente isolado como Anaconda ou Virtualenv também é recomendado.

A tabela seguinte mostra cada ambiente de desenvolvimento abrangido por este artigo, juntamente com prós e contras.

| Ambiente | Vantagens | Contras |
| --- | --- | --- |
| [Instância computora da cloud Azure Machine Learning (pré-visualização)](#compute-instance) | É a maneira mais fácil de começar. Todo o SDK já está instalado no seu espaço de trabalho VM, e os tutoriais portátil estão pré-clonados e prontos a funcionar. | Falta de controlo sobre o seu ambiente de desenvolvimento e dependências. Custo adicional incorrido para o Linux VM (VM pode ser interrompido quando não está em uso para evitar encargos). Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) |
| [Ambiente local](#local) | Controlo total do seu ambiente de desenvolvimento e dependências. Corra com qualquer ferramenta de construção, ambiente ou IDE à sua escolha. | Demora mais tempo a começar. As embalagens SDK necessárias devem ser instaladas e um ambiente também deve ser instalado se ainda não tiver um. |
| [Azure Databricks](#aml-databricks) | Ideal para executar fluxos de trabalho intensivos de aprendizagem automática em larga escala na plataforma Apache Spark escalável. | Exagero para aprendizagem de máquinas experimentais, ou experiências de menor escala e fluxos de trabalho. Custo adicional incorrido para os Tijolos de Dados Azure. Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/databricks/) |
| [A Máquina Virtual da Ciência dos Dados (DSVM)](#dsvm) | Semelhante à instância computacional baseada na nuvem (Python e o SDK estão pré-instalados), mas com ferramentas de ciência de dados populares e machine learning adicionais pré-instaladas. Fácil de escalar e combinar com outras ferramentas personalizadas e fluxos de trabalho. | Uma experiência de início mais lenta em comparação com a instância computacional baseada na nuvem. |

Este artigo também fornece dicas de utilização adicionais para as seguintes ferramentas:

* [Cadernos Jupyter](#jupyter): Se já estiver a usar o Caderno Jupyter, o SDK tem alguns extras que deve instalar.

* [Código](#vscode)do Estúdio Visual : Se utilizar o Código do Estúdio Visual, a [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) inclui um extenso suporte linguístico para Python, bem como funcionalidades para tornar o trabalho com o Azure Machine Learning muito mais conveniente e produtivo.

## <a name="prerequisites"></a>Pré-requisitos

Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md). Um espaço de trabalho é tudo o que precisa para começar com o seu próprio servidor de [portátil baseado na nuvem](#compute-instance), um [DSVM](#dsvm)ou [Tijolos de Dados Azure](#aml-databricks).

Para instalar o ambiente SDK para o seu [computador local,](#local) [o servidor Jupyter Notebook](#jupyter) ou o Código do Estúdio [Visual](#vscode) também precisa:

- Ou o gestor de [pacotes Danaconda](https://www.anaconda.com/download/) ou [Miniconda.](https://conda.io/miniconda.html)

- No Linux ou macOS, você precisa da concha de festa.

    > [!TIP]
    > Se estiver no Linux ou macOS e utilizar uma concha que não seja a bash (por exemplo, zsh) poderá receber erros quando executa alguns comandos. Para contornar este problema, use o `bash` comando para iniciar uma nova concha e executar os comandos lá.

- No Windows, é necessário o pedido de comando ou o pedido de Anaconda (instalado pela Anaconda e pela Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Seu próprio exemplo de computação baseada em nuvem

O caso de computação Azure Machine Learning [(pré-visualização)](concept-compute-instance.md) é uma estação de trabalho azure segura e baseada em nuvem que fornece aos cientistas de dados um servidor de portátil Jupyter, JupyterLab, e um ambiente ML totalmente preparado.

Não há nada para instalar ou configurar para um caso de computação.  Crie um a qualquer momento a partir do seu espaço de trabalho Azure Machine Learning. Forneça apenas um nome e especifique um tipo De VM Azure. Experimente agora com este [Tutorial: ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md)

Saiba mais sobre [os casos de computação.](concept-compute-instance.md)

Para parar de incorrer em encargos computacionais, [pare a instância computacional.](tutorial-1st-experiment-sdk-train.md#clean-up-resources)

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Máquina Virtual de Ciência de Dados

O DSVM é uma imagem personalizada da máquina virtual (VM). É projetado para trabalho de ciência de dados que está pré-configurado com:

  - Pacotes como TensorFlow, PyTorch, Scikit-learn, XGBoost e o Azure Machine Learning SDK
  - Ferramentas populares de ciência de dados, tais como Spark Autónomo e Drill
  - Ferramentas azure como o Azure CLI, AzCopy e Storage Explorer
  - Ambientes de desenvolvimento integrados (IDEs) tais como Visual Studio Code e PyCharm
  - Servidor de caderno de jupyter

O Azure Machine Learning SDK funciona na versão Ubuntu ou Windows do DSVM. Mas se planeia usar o DSVM como alvo de computação também, apenas ubuntu é suportado.

Utilizar o DSVM como um ambiente de desenvolvimento:

1. Crie um DSVM em qualquer um dos seguintes ambientes:

    * O portal Azure:

        * [Criar uma Máquina Virtual de Ciência de Dados do Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Criar uma Máquina Virtual de Ciência de Dados do Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * O Azure CLI:

        > [!IMPORTANT]
        > * Quando utilizar o Azure CLI, tem primeiro de iniciar sessão na subscrição do Azure utilizando o `az login` comando.
        >
        > * Quando utilizar os comandos neste passo, deve fornecer um nome de grupo de recursos, um nome para o VM, um nome de utilizador e uma palavra-passe.

        * Para criar uma Máquina Virtual ubuntu Data Science, utilize o seguinte comando:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Para criar uma Máquina Virtual de Ciência de Dados do Windows, utilize o seguinte comando:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. O SDK de Aprendizagem automática Azure já está instalado no DSVM. Para utilizar o ambiente Conda que contém o SDK, utilize um dos seguintes comandos:

    * Para Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Para windows dSVM:

        ```bash
        conda activate AzureML
        ```

1. Para verificar se pode aceder ao SDK e verificar a versão, utilize o seguinte código Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Para configurar o DSVM para utilizar o seu espaço de trabalho Azure Machine Learning, consulte a secção de ficheiros de [configuração do espaço de trabalho Create.](#workspace)

Para mais informações, consulte [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)de Ciência de Dados .

## <a name="local-computer"></a><a id="local"></a>Computador local

Quando estiver a utilizar um computador local (que também pode ser uma máquina virtual remota), crie um ambiente Anaconda e instale o SDK. Eis um exemplo:

1. Descarregue e instale [a Anaconda](https://www.anaconda.com/distribution/#download-section) (versão Python 3.7) se ainda não a tiver.

1. Abra um pedido de Anaconda e crie um ambiente com os seguintes comandos:

    Executar o seguinte comando para criar o ambiente.

    ```bash
    conda create -n myenv python=3.7.7
    ```

    Em seguida, ativar o ambiente.

    ```bash
    conda activate myenv
    ```

    Este exemplo cria um ambiente usando python 3.7.7, mas quaisquer subversões específicas podem ser escolhidas. A compatibilidade SDK pode não ser garantida com certas versões principais (recomenda-se 3.5+), e é recomendado experimentar uma versão/subversão diferente no seu ambiente Anaconda se tiver erros. Levará vários minutos para criar o ambiente enquanto componentes e pacotes são descarregados.

1. Execute os seguintes comandos no seu novo ambiente para permitir miolos IPython específicos do ambiente. Isto garantirá o comportamento esperado de importação de kernel e pacotes ao trabalhar com os Cadernos Jupyter em ambientes Anaconda:

    ```bash
    conda install notebook ipykernel
    ```

    Em seguida, executar o seguinte comando para criar o núcleo:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Utilize os seguintes comandos para instalar pacotes:

    Este comando instala a base Azure Machine Learning SDK com caderno e `automl` extras. O `automl` extra é uma instalação grande, e pode ser removido dos suportes se não pretender executar experiências automatizadas de aprendizagem automática de máquinas. O `automl` extra também inclui o Azure Machine Learning Data Prep SDK por padrão como dependência.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Se receber uma mensagem de que o PyYAML não pode ser desinstalado, utilize o seguinte comando:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Começando com macOS Catalina, zsh (concha Z) é a casca de login padrão e a concha interativa. Em zsh, use o seguinte comando que escape dos suportes com \\ " ( backslash):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Levará vários minutos para instalar o SDK. Para mais informações sobre as opções de instalação, consulte o guia de [instalação](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Instale outros pacotes para a sua experimentação de aprendizagem automática.

    Utilize qualquer um dos seguintes comandos e substitua-o *\<new package>* pela embalagem que pretende instalar. A instalação de pacotes através `conda install` exige que o pacote faça parte dos canais atuais (novos canais podem ser adicionados na Nuvem De Anaconda).

    ```bash
    conda install <new package>
    ```

    Em alternativa, pode instalar pacotes via `pip` .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

Os Cadernos Jupyter fazem parte do [Projeto Jupyter.](https://jupyter.org/) Eles fornecem uma experiência de codificação interativa onde você cria documentos que misturam código ao vivo com texto narrativo e gráficos. Os Cadernos Jupyter também são uma ótima maneira de partilhar os seus resultados com outros, porque você pode salvar a saída das suas secções de código no documento. Você pode instalar Cadernos Jupyter em uma variedade de plataformas.

O procedimento na secção [de computador local](#local) instala componentes necessários para executar cadernos Jupyter num ambiente Anaconda.

Para ativar estes componentes no seu ambiente Jupyter Notebook:

1. Abra um aviso de Anaconda e ative o seu ambiente.

    ```bash
    conda activate myenv
    ```

1. Clone [o repositório GitHub](https://aka.ms/aml-notebooks) para um conjunto de cadernos de amostra.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lance o servidor Jupyter Notebook com o seguinte comando:

    ```bash
    jupyter notebook
    ```

1. Para verificar se o Jupyter Notebook pode usar o SDK, criar um **novo** caderno, selecione **Python 3** como seu núcleo e, em seguida, executar o seguinte comando numa célula de caderno:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se encontrar problemas de importação de módulos e receber um, certifique-se de que `ModuleNotFoundError` o seu núcleo Jupyter está ligado ao caminho correto para o seu ambiente, executando o seguinte código numa célula de Caderno.

    ```python
    import sys
    sys.path
    ```

1. Para configurar o Caderno Jupyter para utilizar o seu espaço de trabalho Azure Machine Learning, vá à secção de ficheiros de [configuração do espaço de trabalho Create.](#workspace)

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code é um editor de código de plataforma transversal muito popular que suporta um vasto conjunto de linguagens e ferramentas de programação através de extensões disponíveis no [mercado do Estúdio Visual.](https://marketplace.visualstudio.com/vscode) A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) instala a [extensão Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para codificação em todos os tipos de ambientes Python (virtual, Anaconda, etc.). Além disso, fornece funcionalidades de conveniência para trabalhar com recursos de Machine Learning Azure e executar experiências de Machine Learning Azure tudo sem deixar o Visual Studio Code.

Para utilizar o Código de Estúdio Visual para o desenvolvimento:

1. Instale a extensão de Aprendizagem automática Azure para Código de Estúdio Visual, consulte [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para mais informações, consulte [Use Azure Machine Learning para Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Saiba como usar o Código de Estúdio Visual para qualquer tipo de desenvolvimento python, consulte [Começar com Python em VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Para selecionar o ambiente SDK Python contendo o SDK, abra o Código VS e, em seguida, selecione Ctrl+Shift+P (Linux e Windows) ou Command+Shift+P (Mac).
        - A __Paleta de Comando__ abre.

    - Introduza __Python: Selecione Intérprete,__ e, em seguida, selecione o ambiente apropriado

1. Para validar que pode utilizar o SDK, crie um novo ficheiro Python (.py) que contenha o seguinte código:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Executar este código clicando no CodeLens "Executar célula" ou simplesmente premir o shift-enter.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks é um ambiente baseado em Apache Spark na nuvem Azure. Proporciona um ambiente colaborativo baseado em cadernos com cpu ou cluster de computação baseado em GPU.

Como os Tijolos de Dados Azure funcionam com a Azure Machine Learning:
+ Pode treinar um modelo utilizando o Spark MLlib e implementar o modelo para ACI/AKS a partir de dentro dos Databricks Azure.
+ Também pode utilizar capacidades automatizadas de [aprendizagem automática](concept-automated-ml.md) num Azure ML SDK especial com Tijolos de Dados Azure.
+ Pode utilizar os Bricks Azure como alvo de cálculo a partir de um [oleoduto Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Configurar o seu cluster de Databricks

Criar um [cluster de Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Algumas definições só se aplicam se instalar o SDK para aprendizagem automática de máquinas em Databricks.
**Levará alguns minutos para criar o aglomerado.**

Utilize estas definições:

| Definição |Aplica-se a| Valor |
|----|---|---|
| Nome do cluster |sempre| seu nome cluster |
| Runtime do Databricks |sempre|Tempo de execução não-ML 6.5 (scala 2.11, faísca 2.4.3) |
| Versão de Python |sempre| 3 |
| Trabalhadores |sempre| 2 ou mais |
| Tipos de VM do nó do trabalhador <br>(determina o máximo # das iterações simultâneas) |ML Automatizado<br>apenas| VM otimizado de memória preferido |
| Ativar a autoscalcificação |ML Automatizado<br>apenas| Desfaça |

Aguarde até que o aglomerado esteja em funcionamento antes de prosseguir.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instale o SDK correto numa biblioteca de Databricks
Assim que o cluster estiver em funcionamento, [crie uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote SDK de Aprendizagem automática Azure apropriado ao seu cluster.

1. Clique na atual pasta Workspace onde pretende armazenar a biblioteca. Selecione **Criar**  >  **Biblioteca**.

1. Escolha **apenas uma** opção (nenhuma outra instalação SDK é suportada)

   |Extras de pacote SDK &nbsp; &nbsp;|Origem|Nome PyPi &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Para Databricks| Carregar Ovo Python ou PyPI | azureml-sdk[databricks]|
   |Para Databricks -com-<br> capacidades ml automatizadas| Carregar Ovo Python ou PyPI | azureml-sdk[automl]|

   > [!Warning]
   > Não podem ser instalados outros extras sDK. Escolha apenas uma das opções anteriores [databricks] ou [automl].

   * Não selecione **Fixe-o automaticamente a todos os clusters**.
   * Selecione **Anexar** ao lado do nome do cluster.

1. Monitorize os erros até que o estado mude para **anexo,** o que pode demorar vários minutos.  Se este passo falhar:

   Tente reiniciar o seu cluster por:
   1. No painel esquerdo, selecione **Clusters**.
   1. Na tabela, selecione o nome do seu cluster.
   1. No separador **Bibliotecas,** selecione **Reiniciar**.

   Considere também:
   + Na config AutoML, ao utilizar os Tijolos de Dados Azure, adicione os seguintes parâmetros:
       1. ```max_concurrent_iterations```baseia-se no número de nós dos trabalhadores no seu cluster.
        2. ```spark_context=sc```baseia-se no contexto de faísca padrão.
   + Ou, se tiver uma versão SDK antiga, desmarque-a das libs instaladas do cluster e mova-se para o lixo. Instale a nova versão SDK e reinicie o cluster. Se houver algum problema após o reinício, desabote e religue o seu cluster.

Se a instalação foi bem sucedida, a biblioteca importada deve parecer uma destas:

SDK para Databricks **_sem_** machine learning automatizado ![ Azure Machine Learning SDK para Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK para Databricks **COM** machine learning automatizado ![ SDK com machine learning automatizado instalado em Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Começar a explorar

Experimente:
+ Embora muitos cadernos de amostraestejam disponíveis, **apenas [estes cadernos de amostras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funcionam com os Bricks Azure.**

+ Importe estas amostras diretamente do seu espaço de trabalho. Ver abaixo: Selecione Painel de Importação de ![ ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ Importações](./media/how-to-configure-environment/azure-db-import.png)

+ Aprenda a [criar um pipeline com Databricks como a computação de treino.](how-to-create-your-first-pipeline.md)

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Criar um ficheiro de configuração do espaço de trabalho

O ficheiro de configuração do espaço de trabalho é um ficheiro JSON que diz ao SDK como comunicar com o seu espaço de trabalho Azure Machine Learning. O ficheiro é nomeado *config.json,* e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Este ficheiro JSON deve estar na estrutura do diretório que contém os seus scripts Python ou Cadernos Jupyter. Pode estar no mesmo diretório, um subdiretório chamado *.azureml,* ou num directório-mãe.

Para utilizar este ficheiro a partir do seu código, utilize `ws=Workspace.from_config()` . Este código carrega a informação do ficheiro e liga-se ao seu espaço de trabalho.

Pode criar o ficheiro de configuração de três formas:

* **Utilize [ws.write_config:](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** para escrever um ficheiro *config.json.* O ficheiro contém as informações de configuração para o seu espaço de trabalho. Você pode baixar ou copiar o *config.json* para outros ambientes de desenvolvimento.

* **Descarregue o ficheiro**: No [portal Azure,](https://ms.portal.azure.com)selecione **Descarregue config.json** da secção **de visão geral** do seu espaço de trabalho.

     ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* **Crie o ficheiro programáticamente**: No seguinte código, liga-se a um espaço de trabalho fornecendo o ID de subscrição, grupo de recursos e nome do espaço de trabalho. Em seguida, guarda a configuração do espaço de trabalho para o ficheiro:

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

    Este código escreve o ficheiro de configuração para o ficheiro *.azureml/config.json.*

## <a name="next-steps"></a>Próximos passos

- [Treine um modelo](tutorial-train-models-with-aml.md) em Azure Machine Learning com o conjunto de dados MNIST
- Ver o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) referência python
