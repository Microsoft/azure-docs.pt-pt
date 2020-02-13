---
title: Configurar um ambiente de desenvolvimento do Python
titleSuffix: Azure Machine Learning
description: Saiba como configurar seu ambiente de desenvolvimento para Azure Machine Learning. Use ambientes Conda, Crie arquivos de configuração e configure seu próprio servidor de notebook baseado em nuvem, notebooks Jupyter, Azure Databricks, IDEs, editores de código e o Máquina Virtual de Ciência de Dados.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 32db7b19b7ec63135c3359f9685dd767dd0921f5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169853"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Configurar um ambiente de desenvolvimento do Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a configurar um ambiente de desenvolvimento para trabalhar com Azure Machine Learning. Azure Machine Learning é independente da plataforma. O único requisito rígido para seu ambiente de desenvolvimento é o Python 3. Um ambiente isolado como Anaconda ou Virtualenv também é recomendado.

A tabela a seguir mostra cada ambiente de desenvolvimento abordado neste artigo, juntamente com prós e contras.

| Ambiente | Profissionais de TI | Contras |
| --- | --- | --- |
| [Instância computora da cloud Azure Machine Learning (pré-visualização)](#compute-instance) | A maneira mais fácil de começar. O SDK completo já está instalado na VM do seu espaço de trabalho, e os tutoriais do notebook são previamente clonados e prontos para serem executados. | Falta de controle sobre seu ambiente de desenvolvimento e dependências. Custo adicional incorrido para a VM do Linux (a VM pode ser interrompida quando não estiver em uso para evitar cobranças). Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) |
| [Ambiente local](#local) | Controle total do seu ambiente de desenvolvimento e dependências. Execute com qualquer ferramenta de compilação, ambiente ou IDE de sua escolha. | Leva mais tempo para começar. Os pacotes SDK necessários devem ser instalados e um ambiente também deve ser instalado se você ainda não tiver um. |
| [Azure Databricks](#aml-databricks) | Ideal para executar fluxos de trabalho de aprendizado de máquina com uso intensivo em larga escala na plataforma de Apache Spark escalonável. | Um exagero para aprendizado de máquina experimental ou experimentos e fluxos de trabalho de escala menor. Custo adicional incorrido para Azure Databricks. Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/databricks/) |
| [A Máquina Virtual da Ciência dos Dados (DSVM)](#dsvm) | Semelhante à instância computacional baseada na nuvem (Python e o SDK estão pré-instalados), mas com ferramentas de ciência de dados populares e machine learning adicionais pré-instaladas. Fácil de dimensionar e combinar com outras ferramentas e fluxos de trabalho personalizados. | Uma experiência de início mais lenta em comparação com a instância computacional baseada na nuvem. |


Este artigo também fornece dicas de uso adicionais para as seguintes ferramentas:

* [Cadernos Jupyter](#jupyter): Se já estiver a usar o Caderno Jupyter, o SDK tem alguns extras que deve instalar.

* [Código](#vscode)do Estúdio Visual : Se utilizar o Código do Estúdio Visual, a [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) inclui um extenso suporte linguístico para Python, bem como funcionalidades para tornar o trabalho com o Azure Machine Learning muito mais conveniente e produtivo.

## <a name="prerequisites"></a>Pré-requisitos

Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md). Um espaço de trabalho é tudo o que precisa para começar com o seu próprio servidor de [portátil baseado na nuvem](#compute-instance), um [DSVM](#dsvm)ou [Tijolos de Dados Azure](#aml-databricks).

Para instalar o ambiente SDK para o seu [computador local,](#local) [o servidor Jupyter Notebook](#jupyter) ou o Código do Estúdio [Visual](#vscode) também precisa:

- Ou o gestor de [pacotes Danaconda](https://www.anaconda.com/download/) ou [Miniconda.](https://conda.io/miniconda.html)

- No Linux ou no macOS, você precisa do shell bash.

    > [!TIP]
    > Se você estiver no Linux ou no macOS e usar um shell diferente do bash (por exemplo, zsh), você poderá receber erros ao executar alguns comandos. Para contornar este problema, use o comando `bash` para iniciar uma nova concha e executar os comandos lá.

- No Windows, terá da linha de comandos ou prompt Anaconda (por Anaconda e Miniconda instalado).

## <a id="compute-instance"></a>Seu próprio exemplo de computação baseada em nuvem

O caso de computação Azure Machine Learning [(pré-visualização)](concept-compute-instance.md) é uma estação de trabalho azure segura e baseada em nuvem que fornece aos cientistas de dados um servidor de portátil Jupyter, JupyterLab, e um ambiente ML totalmente preparado.

Não há nada para instalar ou configurar para uma instância de computação.  Crie uma a qualquer momento em seu espaço de trabalho Azure Machine Learning. Forneça apenas um nome e especifique um tipo de VM do Azure. Experimente agora com este [Tutorial: ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md)


Saiba mais sobre [os casos de computação.](concept-compute-instance.md)

Para parar de incorrer em encargos computacionais, [pare a instância computacional.](tutorial-1st-experiment-sdk-train.md#clean-up-resources)

## <a id="dsvm"></a>Máquina Virtual da Ciência dos Dados

O DSVM é uma imagem de VM (máquina virtual) personalizada. Ele foi projetado para o trabalho de ciência de dados pré-configurado com:

  - Pacotes como TensorFlow, PyTorch, Scikit-learn, XGBoost e o SDK Azure Machine Learning
  - Ferramentas de ciência de dados populares, como Spark standalone e Drill
  - Ferramentas do Azure, como o CLI do Azure, AzCopy e Gerenciador de Armazenamento
  - IDEs (ambientes de desenvolvimento integrados) como Visual Studio Code e PyCharm
  - Servidor de bloco de notas do Jupyter

O SDK do Azure Machine Learning funciona na versão do Ubuntu ou do Windows do DSVM. Mas se você planeja usar o DSVM como um destino de computação também, apenas o Ubuntu tem suporte.

Para usar o DSVM como um ambiente de desenvolvimento:

1. Crie um DSVM em um dos seguintes ambientes:

    * O portal do Azure:

        * [Criar uma máquina virtual ubuntu data science](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Criar uma máquina virtual de ciência de dados do Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * O CLI do Azure:

        > [!IMPORTANT]
        > * Quando utilizar o Azure CLI, tem de iniciar sessão na subscrição do Azure utilizando o comando `az login`.
        >
        > * Ao usar os comandos nesta etapa, você deve fornecer um nome de grupo de recursos, um nome para a VM, um nome de usuário e uma senha.

        * Para criar um Máquina Virtual de Ciência de Dados Ubuntu, use o seguinte comando:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Para criar um Máquina Virtual de Ciência de Dados do Windows, use o seguinte comando:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. O SDK do Azure Machine Learning já está instalado no DSVM. Para utilizar o ambiente de Conda que contém o SDK, utilize um dos seguintes comandos:

    * Para o Ubuntu DSVM:

        ```shell
        conda activate py36
        ```

    * Para o Windows DSVM:

        ```shell
        conda activate AzureML
        ```

1. Para verificar se pode acessar o SDK e verificar a versão, utilize o seguinte código de Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Para configurar o DSVM para utilizar o seu espaço de trabalho Azure Machine Learning, consulte a secção de ficheiros de [configuração do espaço de trabalho Create.](#workspace)

Para mais informações, consulte [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)de Ciência de Dados .

## <a id="local"></a>Computador local

Quando você estiver usando um computador local (que também pode ser uma máquina virtual remota), crie um ambiente Anaconda e instale o SDK. Segue-se um exemplo:

1. Descarregue e instale [a Anaconda](https://www.anaconda.com/distribution/#download-section) (versão Python 3.7) se ainda não a tiver.

1. Abra um prompt do Anaconda e crie um ambiente com os seguintes comandos:

    Execute o comando a seguir para criar o ambiente.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Em seguida, ative o ambiente.

    ```shell
    conda activate myenv
    ```

    Este exemplo cria um ambiente usando Python 3.6.5, mas qualquer subversão específica pode ser escolhida. A compatibilidade do SDK pode não ser garantida com determinadas versões principais (o 3.5 + é recomendado) e é recomendável tentar uma versão/subversão diferente em seu ambiente Anaconda se você encontrar erros. Levará vários minutos para criar o ambiente enquanto os componentes e pacotes são baixados.

1. Execute os seguintes comandos em seu novo ambiente para habilitar kernels IPython específicos do ambiente. Isso garantirá o comportamento esperado de importação de kernel e pacote ao trabalhar com notebooks Jupyter em ambientes Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Em seguida, execute o seguinte comando para criar o kernel:

    ```shell
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Use os seguintes comandos para instalar pacotes do:

    Este comando instala a base Azure Machine Learning SDK com caderno e extras `automl`. O `automl` extra é uma instalação grande, e pode ser removido dos suportes se não pretender executar experiências automatizadas de aprendizagem automática de máquinas. O `automl` extra também inclui o Azure Machine Learning Data Prep SDK por padrão como dependência.

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Se você receber uma mensagem informando que PyYAML não pode ser desinstalado, use o seguinte comando em vez disso:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * A partir do macOS Catalina, o zsh (Z Shell) é o Shell de logon padrão e o shell interativo. Em zsh, use o seguinte comando que escape dos suportes com "\\" (backslash):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   Levará vários minutos para instalar o SDK. Para mais informações sobre as opções de instalação, consulte o guia de [instalação](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Instale outros pacotes para sua experimentação do Machine Learning.

    Utilize qualquer um dos seguintes comandos e substitua *\<novo pacote>* com a embalagem que pretende instalar. Instalar pacotes via `conda install` requer que o pacote faça parte dos canais atuais (novos canais podem ser adicionados na Nuvem De Anaconda).

    ```shell
    conda install <new package>
    ```

    Em alternativa, pode instalar pacotes via `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Cadernos jupyter

Os Cadernos Jupyter fazem parte do [Projeto Jupyter.](https://jupyter.org/) Eles fornecem uma experiência interativa de codificação onde criar documentos que misturam código em direto com texto narrativo e gráficos. Os notebooks Jupyter também são uma ótima maneira de compartilhar seus resultados com outras pessoas, pois você pode salvar a saída de suas seções de código no documento. Pode instalar o Jupyter Notebooks numa variedade de plataformas.

O procedimento na secção [de computador local](#local) instala componentes necessários para executar cadernos Jupyter num ambiente Anaconda.

Para habilitar esses componentes em seu ambiente de Jupyter Notebook:

1. Abra um prompt do Anaconda e ative seu ambiente.

    ```shell
    conda activate myenv
    ```

1. Clone [o repositório GitHub](https://aka.ms/aml-notebooks) para um conjunto de cadernos de amostra.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de Jupyter Notebook com o seguinte comando:

    ```shell
    jupyter notebook
    ```

1. Para verificar se o Jupyter Notebook pode usar o SDK, criar um **novo** caderno, selecione **Python 3** como seu núcleo e, em seguida, executar o seguinte comando numa célula de caderno:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Se encontrar problemas de importação de módulos e receber um `ModuleNotFoundError`, certifique-se de que o seu núcleo Jupyter está ligado ao caminho correto para o seu ambiente, executando o seguinte código numa célula de Caderno.

    ```python
    import sys
    sys.path
    ```

1. Para configurar o Caderno Jupyter para utilizar o seu espaço de trabalho Azure Machine Learning, vá à secção de ficheiros de [configuração do espaço de trabalho Create.](#workspace)


### <a id="vscode"></a>Código de estúdio visual

Visual Studio Code é um editor de código de plataforma transversal muito popular que suporta um vasto conjunto de linguagens e ferramentas de programação através de extensões disponíveis no [mercado do Estúdio Visual.](https://marketplace.visualstudio.com/vscode) A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) instala a [extensão Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para codificação em todos os tipos de ambientes Python (virtual, Anaconda, etc.). Além disso, ele fornece recursos de conveniência para trabalhar com Azure Machine Learning recursos e executar Azure Machine Learning experimentos sem sair Visual Studio Code.

Para usar Visual Studio Code para desenvolvimento:

1. Instale a extensão de Aprendizagem automática Azure para Código de Estúdio Visual, consulte [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Para mais informações, consulte [Use Azure Machine Learning para Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Saiba como usar o Código de Estúdio Visual para qualquer tipo de desenvolvimento python, consulte [Começar com Python em VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Para selecionar o ambiente Python do SDK que contém o SDK, abra VS Code e, em seguida, selecione CTRL + SHIFT + P (Linux e Windows) ou Command + Shift + P (Mac).
        - A __Paleta de Comando__ abre.

    - Introduza __Python: Selecione Intérprete,__ e, em seguida, selecione o ambiente apropriado

1. Para validar que você pode usar o SDK, crie um novo arquivo Python (. py) que contenha o seguinte código:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Execute esse código clicando no CodeLens "executar célula" ou simplesmente pressione Shift-Enter.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks é um ambiente baseado em Apache Spark na nuvem do Azure. Ele fornece um ambiente de colaboração baseado em notebook com CPU ou cluster de computação baseado em GPU.

Como Azure Databricks funciona com Azure Machine Learning:
+ Você pode treinar um modelo usando o Spark MLlib e implantar o modelo em ACI/AKS de dentro Azure Databricks.
+ Também pode utilizar capacidades automatizadas de [aprendizagem automática](concept-automated-ml.md) num Azure ML SDK especial com Tijolos de Dados Azure.
+ Pode utilizar os Bricks Azure como alvo de cálculo a partir de um [oleoduto Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Configurar o cluster do databricks

Criar um [cluster de Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Algumas configurações se aplicam somente se você instalar o SDK do Machine Learning automatizado no databricks.
**Levará alguns minutos para criar o aglomerado.**

Utilize estas definições:

| Definição |Aplica-se a| Valor |
|----|---|---|
| Nome do cluster |sempre| yourclustername |
| Runtime do Databricks |sempre|Tempo de execução não ML 6,0 (escala 2,11, Spark 2.4.3) |
| Versão de Python |sempre| 3 |
| Trabalhadores |sempre| 2 ou superior |
| Tipos de VM de nó de trabalho <br>(determina o número máximo de iterações simultâneas) |ML Automatizado<br>somente| VM com otimização de memória preferencial |
| Ativar a autoscalcificação |ML Automatizado<br>somente| Desfaça |

Aguarde até que o cluster esteja em execução antes de continuar.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instalar o SDK correto em uma biblioteca do databricks
Assim que o cluster estiver em funcionamento, [crie uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote SDK de Aprendizagem automática Azure apropriado ao seu cluster.

1. Clique com o botão direito do mouse na pasta do espaço de trabalho atual onde você deseja armazenar a biblioteca. Selecione **Criar** > **Biblioteca**.

1. Escolha **apenas uma** opção (nenhuma outra instalação SDK é suportada)

   |Pacote de&nbsp;SDK&nbsp;extras|Origem|Nome&nbsp;PyPi&nbsp; &nbsp;&nbsp;&nbsp; de &nbsp; &nbsp;|
   |----|---|---|
   |Para databricks| Carregar ovo Python ou PyPI | azureml-sdk[databricks]|
   |Para databricks-com-<br> recursos de ML automatizados| Carregar ovo Python ou PyPI | azureml-sdk[automl]|

   > [!Warning]
   > Nenhum outro adicional do SDK pode ser instalado. Escolha apenas uma das opções anteriores [databricks] ou [automl].

   * Não selecione **Fixe-o automaticamente a todos os clusters**.
   * Selecione **Anexar** ao lado do nome do cluster.

1. Monitorize os erros até que o estado mude para **anexo,** o que pode demorar vários minutos.  Se esta etapa falhar:

   Tente reiniciar o cluster da:
   1. No painel esquerdo, selecione **Clusters**.
   1. Na tabela, selecione o nome do cluster.
   1. No separador **Bibliotecas,** selecione **Reiniciar**.

   Considere também:
   + Na configuração do AutoML, ao usar Azure Databricks adicione os seguintes parâmetros:
       1. ```max_concurrent_iterations``` baseia-se no número de nós dos trabalhadores no seu agrupamento.
        2. ```spark_context=sc``` baseia-se no contexto de faísca padrão.
   + Ou, se você tiver uma versão antiga do SDK, desmarque-a do bibliotecas instalado do cluster e mude para Trash. Instale a nova versão do SDK e reinicie o cluster. Se houver um problema após a reinicialização, desanexe e anexe novamente o cluster.

Se a instalação tiver sido bem-sucedida, a biblioteca importada deverá ser semelhante a uma destas:

SDK para Databricks **_sem_** machine learning automatizado ![Azure Machine Learning SDK para Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK para Databricks **com** aprendizagem automática de máquinas ![SDK com machine learning automatizado instalado em Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Comece a explorar

Experimente:
+ Embora muitos cadernos de amostraestejam disponíveis, **apenas [estes cadernos de amostras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funcionam com os Bricks Azure.**

+ Importe esses exemplos diretamente do seu espaço de trabalho. Ver abaixo: ![Selecione import](./media/how-to-configure-environment/azure-db-screenshot.png)
![Import Panel](./media/how-to-configure-environment/azure-db-import.png)

+ Aprenda a [criar um pipeline com Databricks como a computação de treino.](how-to-create-your-first-pipeline.md)

## <a id="workspace"></a>Criar um ficheiro de configuração do espaço de trabalho

O arquivo de configuração do espaço de trabalho é um arquivo JSON que informa ao SDK como se comunicar com seu espaço de trabalho Azure Machine Learning. O ficheiro é nomeado *config.json,* e tem o seguinte formato:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Esse arquivo JSON deve estar na estrutura de diretório que contém seus scripts Python ou notebooks Jupyter. Pode estar no mesmo diretório, um subdiretório chamado *.azureml,* ou num directório-mãe.

Para utilizar este ficheiro a partir do seu código, utilize `ws=Workspace.from_config()`. Esse código carrega as informações do arquivo e liga-se a sua área de trabalho.

Você pode criar o arquivo de configuração de três maneiras:

* **Utilize [ws.write_config:](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** para escrever um ficheiro *config.json.* O arquivo contém as informações de configuração para seu espaço de trabalho. Você pode baixar ou copiar o *config.json* para outros ambientes de desenvolvimento.

* **Descarregue o ficheiro**: No [portal Azure,](https://ms.portal.azure.com)selecione **Descarregue config.json** da secção **de visão geral** do seu espaço de trabalho.

     ![Portal do Azure](./media/how-to-configure-environment/configure.png)

* **Crie o ficheiro programáticamente**: No seguinte código, liga-se a um espaço de trabalho fornecendo o ID de subscrição, grupo de recursos e nome do espaço de trabalho. Em seguida, ele salva a configuração do espaço de trabalho no arquivo:

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


## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo](tutorial-train-models-with-aml.md) em Azure Machine Learning com o conjunto de dados MNIST
- Ver o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) referência python
