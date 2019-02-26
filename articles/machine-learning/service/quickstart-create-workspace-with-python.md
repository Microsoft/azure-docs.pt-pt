---
title: 'Início rápido: Comece a utilizar em Python'
titleSuffix: Azure Machine Learning service
description: Introdução ao serviço Azure Machine Learning em Python. Utilize o Python SDK para criar uma área de trabalho, o que é o bloco fundamental na cloud que utilizar para testar, preparar e implementar modelos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/22/2019
ms.custom: seodec18
ms.openlocfilehash: 513df9f68fdd54b5dc90e57bd8389688c46bf615
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804251"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Início rápido: Utilizar o Python SDK para começar a utilizar com o Azure Machine Learning

Neste artigo, vai utilizar o Azure Machine Learning SDK para Python 3 para criar e, em seguida, utilizar um serviço do Azure Machine Learning [área de trabalho](concept-azure-machine-learning-architecture.md). A área de trabalho é o bloco fundamental na cloud que utilizar para testar, preparar e implementar modelos de aprendizagem automática com o Machine Learning.

Começar através da configuração de seu próprio ambiente de Python e o servidor de bloco de notas do Jupyter. Para executá-lo sem instalação, consulte [início rápido: Utilizar o portal do Azure para começar a utilizar com o Azure Machine Learning](quickstart-get-started.md). 

Ver uma vídeo versão deste início rápido:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Neste início rápido, irá:

* Instale o SDK de Python.
* Criar uma área de trabalho na sua subscrição do Azure.
* Criar um ficheiro de configuração para essa área de trabalho, para utilização posterior noutros blocos de notas e scripts.
* Escrever código que regista os valores dentro da área de trabalho.
* Ver os valores registados na área de trabalho.

Criar uma área de trabalho e um ficheiro de configuração para utilizar como pré-requisitos para outros tutoriais de Machine Learning e artigos de instruções. Tal como acontece com outros serviços do Azure, determinados limites e quotas estão associadas com Machine Learning. [Saiba mais sobre quotas e como pedir mais.](how-to-manage-quotas.md)

Os seguintes recursos do Azure são adicionados automaticamente à sua área de trabalho quando estiverem disponíveis regionalmente:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/)

>[!NOTE]
> Código neste artigo requer o Azure Machine Learning SDK versão 1.0.2 ou posterior e foi testado com a versão 1.0.8.


Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

## <a name="install-the-sdk"></a>Instalar o SDK

> [!IMPORTANT]
> Ignore esta secção se utilizar uma máquina de Virtual de ciência de dados do Azure ou o Azure Databricks.
> * Máquinas de virtuais de ciência de dados do Azure criadas após a 27 de Setembro de 2018 são fornecidos com o SDK de Python pré-instalado.
> * No ambiente do Azure Databricks, utilize o [passos de instalação do Databricks](how-to-configure-environment.md#azure-databricks) em vez disso.

Antes de instalar o SDK, recomendamos que crie um ambiente Python isolado. Embora este artigo usa [Miniconda](https://docs.conda.io/en/latest/miniconda.html), também pode utilizar completo [Anaconda](https://www.anaconda.com/) instalado ou [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

As instruções neste guia de introdução irão instalar todos os pacotes que necessários para executar os blocos de notas do guia de introdução e tutorial.  Outros blocos de notas de exemplo podem exigir a instalação dos componentes adicionais.  Para obter mais informações sobre estes componentes, consulte [instalar o SDK do Azure Machine Learning para o Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Instalar o Miniconda

[Baixe e instale o Miniconda](https://docs.conda.io/en/latest/miniconda.html). Selecione a versão 3.7 de Python para instalar. Não selecione a versão Python 2.x.  

### <a name="create-an-isolated-python-environment"></a>Criar um ambiente Python isolado

1. Abra uma janela da linha de comandos, em seguida, crie um novo ambiente de conda com o nome *myenv* e instalar o Python 3.6.5. Não é totalmente funcional no Python 3.7 do Azure irá do SDK de Aprendizado de máquina funcionam com o Python 3.5.2 ou posterior, mas de componentes de aprendizagem automática.  Irá demorar alguns minutos a criar o ambiente, enquanto os componentes e os pacotes são transferidos.

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Ative o ambiente.

    ```shell
    conda activate myenv
    ```

1. Ative kernels ipython específicos do ambiente:

    ```shell
    conda install notebook ipykernel
    ```

    Em seguida, crie o kernel:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Instalar o SDK

1. No ambiente de conda ativados, instale os componentes principais do SDK do Aprendizado de máquina com capacidades de bloco de notas do Jupyter.  A instalação demora alguns minutos para concluir com base na configuração do seu computador.

  ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Para utilizar neste ambiente para os tutoriais do Azure Machine Learning, instale estes pacotes.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Para utilizar neste ambiente para os tutoriais do Azure Machine Learning, instalação de componentes de aprendizagem automática.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Crie a sua área de trabalho num bloco de notas do Jupyter com o SDK de Python.

1. Criar e/ou cd para o diretório que pretende utilizar para o guia de introdução e tutoriais.

1. Para iniciar o bloco de notas do Jupyter, introduza este comando:

    ```shell
    jupyter notebook
    ```

1. Na janela do browser, crie um novo bloco de notas com o kernel `Python 3` predefinido. 

1. Para apresentar a versão do SDK, introduza e, em seguida, execute o seguinte código de Python numa célula de bloco de notas:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Encontrar um valor para o `<azure-subscription-id>` parâmetro na [lista de subscrições no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Utilize qualquer subscrição em que a sua função seja proprietário ou contribuidor.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   Quando executar o código, poderá ser-lhe pedido para iniciar sessão na sua conta do Azure. Depois de iniciar sessão, o token de autenticação ficará em cache localmente.

1. Para ver a área de trabalho de detalhes, como o armazenamento associado, o registo de contentor e o Cofre de chaves, introduza o seguinte código:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Escrever um ficheiro de configuração

Guarde os detalhes da sua área de trabalho num arquivo de configuração para o diretório atual. Este ficheiro é chamado *aml_config\config.json*.  

Este ficheiro de configuração da área de trabalho torna mais fácil carregar a mesma área de trabalho mais tarde. Pode carregá-la com outros blocos de notas e scripts no mesmo diretório ou num subdiretório.  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Isso `write_config()` chamada à API cria o ficheiro de configuração no diretório atual. O *config* arquivo contém o seguinte:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Utilizar a área de trabalho

Execute alguns códigos que usam as APIs básicas do SDK para monitorizar as execuções de experimentações:

1. Crie uma experimentação na área de trabalho.
1. Inicie a sessão de um valor único para a experimentação.
1. Inicie a sessão de uma lista de valores para a experimentação.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Ver resultados registados
Quando a execução for concluída, pode ver a execução da experimentação no portal do Azure. Para imprimir um URL que navega para os resultados para a última execução, utilize o seguinte código:

```python
print(run.get_portal_url())
```

Utilize a ligação para ver os valores registados no portal do Azure no seu browser.

![Valores com sessão iniciada no portal do Azure](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Limpar recursos 
>[!IMPORTANT]
>Pode usar os recursos que criou aqui como pré-requisitos para outros tutoriais de Machine Learning e referência artigos.

Se não planeja usar os recursos que criou neste artigo, eliminá-los para evitar incorrer em quaisquer custos.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou os recursos que necessários para experimentar e implementar modelos. Executou o código num bloco de notas e explorou o histórico de execução para o código na sua área de trabalho na cloud.

> [!div class="nextstepaction"]
> [Tutorial: Preparar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)

Também pode explorar [exemplos mais avançados no GitHub](https://aka.ms/aml-notebooks).
