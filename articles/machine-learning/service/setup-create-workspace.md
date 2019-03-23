---
title: Criar uma área de trabalho
titleSuffix: Azure Machine Learning service
description: Utilize o portal do Azure, o SDK, um modelo ou a CLI para criar a sua área de trabalho do serviço do Azure Machine Learning. Esta área de trabalho fornece um local centralizado para trabalhar com todos os artefactos que criar quando utilizar o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 03/21/2019
ms.openlocfilehash: 2c3b63e671240a239dc2037ce56e56af4a8f95e7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369167"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Criar uma área de trabalho do serviço do Azure Machine Learning

Para utilizar o serviço Azure Machine Learning, é necessário um [ **área de trabalho do serviço Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace).  Esta área de trabalho é o recurso de nível superior para o serviço e fornece-lhe um local centralizado para trabalhar com todos os artefactos de que criar. 

Neste artigo, saiba como criar uma área de trabalho usando qualquer um dos seguintes métodos: 
* O [portal do Azure](#portal) interface
* O [do Azure Machine Learning SDK para Python](#sdk)
* Um [modelo Azure Resource Manager](#template)
* O [do Azure Machine Learning CLI](#cli)

A área de trabalho, criar utilizando os passos aqui em pode ser usada como um pré-requisito para outros tutoriais e artigos de instruções. 

Quando cria uma área de trabalho os seguintes recursos do Azure são adicionados automaticamente (se estiverem disponíveis regional):
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Tal como acontece com outros serviços do Azure, determinados limites e quotas estão associadas com Machine Learning. [Saiba mais sobre quotas e como pedir mais.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Pré-requisitos
Para criar uma área de trabalho, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

## <a name="portal"></a> Portal do Azure

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Não importa como ele foi criado, pode ver a área de trabalho do [portal do Azure](https://portal.azure.com/).  Ver [ver uma área de trabalho](how-to-manage-workspace.md#view) para obter detalhes.

## <a name="sdk"></a> SDK Python

Crie a sua área de trabalho com o SDK de Python. Primeiro tem de instalar o SDK.

> [!IMPORTANT]
> Ignorar instalação do SDK, se utilizar uma máquina de Virtual de ciência de dados do Azure ou o Azure Databricks.
> * Máquinas de virtuais de ciência de dados do Azure criadas após a 27 de Setembro de 2018 são fornecidos com o SDK de Python pré-instalado. Ignorar a instalação e começar com [crie uma área de trabalho com o SDK](#sdk-create).
> * No ambiente do Azure Databricks, utilize o [passos de instalação do Databricks](how-to-configure-environment.md#azure-databricks) em vez disso.

>[!NOTE]
> Utilize estas instruções para instalar e utilizar o SDK do computador local. Para utilizar o Jupyter numa máquina virtual remota, configure o remoto área de trabalho ou X sessão de terminal.

Antes de instalar o SDK, recomendamos que crie um ambiente Python isolado. Embora este artigo usa [Miniconda](https://docs.conda.io/en/latest/miniconda.html), também pode utilizar completo [Anaconda](https://www.anaconda.com/) instalado ou [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

As instruções neste artigo, irão instalar todos os pacotes que necessários para executar os blocos de notas do guia de introdução e tutorial.  Outros blocos de notas de exemplo podem exigir a instalação dos componentes adicionais.  Para obter mais informações sobre estes componentes, consulte [instalar o SDK do Azure Machine Learning para o Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

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

1. No ambiente de conda ativados, instale os componentes principais do SDK do Aprendizado de máquina com capacidades de bloco de notas do Jupyter. A instalação demora alguns minutos para concluir com base na configuração do seu computador.

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

> [!IMPORTANT]
> Em algumas ferramentas de linha de comando, poderá ter de adicionar o entre aspas duplas da seguinte forma:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> Crie uma área de trabalho com o SDK

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


### <a name="write-a-configuration-file"></a>Escrever um ficheiro de configuração

Guarde os detalhes da sua área de trabalho num arquivo de configuração para o diretório atual. Este ficheiro é chamado *aml_config/config.json*.  

Este ficheiro de configuração da área de trabalho torna mais fácil carregar a mesma área de trabalho mais tarde. Pode carregá-lo com outros blocos de notas e scripts no mesmo diretório ou subdiretório com o código `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Isso `write_config()` chamada à API cria o ficheiro de configuração no diretório atual. O *config* arquivo contém o seguinte:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Para utilizar a sua área de trabalho em scripts de Python ou blocos de notas do Jupyter localizados em outros diretórios, copie esse arquivo para esse diretório. O ficheiro pode estar no mesmo diretório, um subdiretório nomeado *aml_config*, ou num diretório principal.

## <a name="resource-manager-template"></a>Modelo do Resource manager

Para criar uma área de trabalho com um modelo, consulte [criar uma área de trabalho do serviço do Azure Machine Learning utilizando um modelo](how-to-create-workspace-template.md)

## <a name="cli"></a>CLI

Para criar uma área de trabalho com a CLI, veja [utilizar a extensão da CLI para o serviço Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Limpar recursos 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

* Não importa como ele foi criado, pode ver a área de trabalho do [portal do Azure](https://portal.azure.com/).  Ver [ver uma área de trabalho](how-to-manage-workspace.md#view) para obter detalhes.

* Experimente a sua área de trabalho com estes inícios rápidos e tutoriais.

    * Início rápido: [Executar o bloco de notas do Jupyter na cloud](quickstart-run-cloud-notebook.md).
    * Início rápido: [Executar o bloco de notas do Jupyter no seu próprio servidor](quickstart-run-local-notebook.md).
    * Tutorial de duas partes: [Train](tutorial-train-models-with-aml.md) e [implementar](tutorial-deploy-models-with-aml.md) um modo de classificação de imagem.
    * Tutorial de duas partes: [Preparar dados](tutorial-data-prep.md) e [utilizar a aprendizagem automática](tutorial-auto-train-models.md) para criar um modelo de regressão.

* Saiba mais sobre como [configurar um ambiente de desenvolvimento](how-to-configure-environment.md).

* Saiba mais sobre o [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk).
