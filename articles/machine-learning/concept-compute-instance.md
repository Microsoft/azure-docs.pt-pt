---
title: O que é uma instância de computação Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a instância de computação Azure Machine Learning, uma estação de trabalho totalmente gerenciada em nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 6bba4414390efa6e07be6c253fe55f1638d414dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974097"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é uma instância de computação Azure Machine Learning?

Uma instância de computação Azure Machine Learning (versão prévia) é uma estação de trabalho baseada em nuvem totalmente gerenciada para cientistas de dados. 

As instâncias de computação facilitam a introdução ao desenvolvimento de Azure Machine Learning, além de fornecer recursos de gerenciamento e preparação empresarial para os administradores de ti.  

Use uma instância de computação como seu ambiente de desenvolvimento totalmente configurado e gerenciado na nuvem.

As instâncias de computação normalmente são usadas como ambientes de desenvolvimento.  Eles também podem ser usados como um destino de computação para treinamento e inferência para desenvolvimento e teste.  Para tarefas grandes, um [cluster de computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós é uma opção de destino de computação melhor.

> [!NOTE]
> Atualmente, as instâncias de computação estão disponíveis somente para espaços de trabalho com uma região de **EUA Central norte** ou **sul do Reino Unido**, com suporte para outras regiões em breve.
>Se o seu espaço de trabalho estiver em qualquer outra região, você poderá continuar a criar e usar uma [VM do bloco de anotações](concept-compute-instance.md#notebookvm) . 

## <a name="why-use-a-compute-instance"></a>Por que usar uma instância de computação?

Uma instância de computação é uma estação de trabalho totalmente gerenciada baseada em nuvem otimizada para seu ambiente de desenvolvimento do Machine Learning. Ele oferece os seguintes benefícios:

|Principais vantagens||
|----|----|
|Produtividade|Os cientistas de dados podem criar e implantar modelos usando blocos de anotações integrados e as seguintes ferramentas em seu navegador da Web:<br/>-Jupyter<br/>-JupyterLab<br/>-RStudio|
|Gerenciado & seguro|Reduza a superfície de segurança e adicione conformidade com os requisitos de segurança corporativa. As instâncias de computação fornecem políticas de gerenciamento robustas e configurações de rede seguras, como:<br/><br/>-Provisionamento automático de modelos do Resource Manager ou SDK do Azure Machine Learning<br/>[controle de acesso baseado em função (RBAC)](/azure/role-based-access-control/overview) - <br/>[suporte à rede Virtual](how-to-enable-virtual-network.md#compute-instance) - <br/>-Política SSH para habilitar/desabilitar o acesso SSH|
|&nbsp;pré-configurado ou&nbsp;ML|Economize tempo nas tarefas de instalação com pacotes ML pré-configurados e atualizados, estruturas de aprendizado profundo, drivers de GPU.|
|Totalmente personalizável|Amplo suporte para tipos de VM do Azure, incluindo GPUs e personalização de baixo nível persistente, como a instalação de pacotes e drivers, torna os cenários avançados uma Breeze. |

## <a name="contents"></a>Ferramentas e ambientes

Azure Machine Learning instância de computação permite que você crie, treine e implante modelos em uma experiência de notebook totalmente integrada em seu espaço de trabalho.


Essas ferramentas e ambientes são instalados na instância de computação: 

|Ferramentas gerais & ambientes|Detalhes|
|----|:----:|
|Controladores|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Biblioteca Intel MPI||
|CLI do Azure ||
|Exemplos de Azure Machine Learning ||
|Azure Machine Learning mecanismo de EDAT ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|Ferramentas do **R** & ambientes|Detalhes|
|----|:----:|
|RStudio Server Open Source Edition||
|Kernel do R||
|Azure Machine Learning SDK para R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Exemplos de SDK|

|Ferramentas **PYTHON** & ambientes|Detalhes|
|----|----|
|Anaconda Python||
|Jupyter e extensões||
|Jupyterlab e extensões||
|Visual Studio Code ||
[Do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>de PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Outros pacotes PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacotes Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacotes de aprendizado profundo|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacotes ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Exemplos de SDK do Azure Machine Learning Python & R||

Os pacotes do Python são todos instalados no ambiente **python 3,6-AzureML** .  

As instâncias de computação normalmente são usadas como ambientes de desenvolvimento.  Eles também podem ser usados como um destino de computação para treinamento e inferência para desenvolvimento e teste.  Para tarefas grandes, um [cluster de computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós é uma opção de destino de computação melhor.

### <a name="installing-packages"></a>Instalar os pacotes

Você pode instalar pacotes diretamente em um Jupyter notebook ou RStudio:

* RStudio use a guia **pacotes** na parte inferior direita ou a guia **console** na parte superior esquerda.  
* Python: Adicione o código de instalação e execute em uma célula do Jupyter notebook.

Ou você pode acessar uma janela de terminal de qualquer uma das seguintes maneiras:

* RStudio: selecione a guia **terminal** na parte superior esquerda.
* Laboratório de Jupyter: selecione o bloco **terminal** sob o **outro** cabeçalho na guia iniciador.
* Jupyter: selecione **novo Terminal >** no canto superior direito na guia arquivos.
* SSH para o computador.  Em seguida, instale os pacotes do Python no ambiente **python 3,6-AzureML** .  Instale os pacotes do R no ambiente do **r** .

## <a name="accessing-files"></a>Acessando arquivos

Os blocos de anotações e os scripts do R são armazenados na conta de armazenamento padrão do seu espaço de trabalho no compartilhamento de arquivos do Azure.  Esses arquivos estão localizados no diretório "arquivos de usuário". Esse armazenamento facilita o compartilhamento de blocos de anotações entre instâncias de computação. A conta de armazenamento também mantém seus blocos de anotações preservados com segurança quando você interrompe ou exclui uma instância de computação.

A conta de compartilhamento de arquivos do Azure do seu espaço de trabalho é montada como uma unidade na instância de computação. Essa unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio.

Os arquivos no compartilhamento de arquivos podem ser acessados de todas as instâncias de computação no mesmo espaço de trabalho. As alterações nesses arquivos na instância de computação serão mantidas de modo confiável de volta para o compartilhamento de arquivos.

Você também pode clonar os exemplos mais recentes do Azure Machine Learning em sua pasta no diretório arquivos do usuário no compartilhamento de arquivos do espaço de trabalho.

Gravar arquivos pequenos pode ser mais lento em unidades de rede do que gravar na própria VM.  Se você estiver escrevendo muitos arquivos pequenos, tente usar um diretório diretamente na instância de computação, como um diretório `/tmp`. Observe que esses arquivos não estarão acessíveis de outras instâncias de computação no espaço de trabalho.

## <a name="managing-a-compute-instance"></a>Gerenciando uma instância de computação

Em seu espaço de trabalho no Azure Machine Learning Studio, selecione **computação**e, em seguida, selecione **instância de computação** na parte superior.

![Gerenciar uma instância de computação](./media/concept-compute-instance/manage-compute-instance.png)

Você pode executar as seguintes ações:

* Crie uma instância de computação. Especifique o nome, o tipo de VM do Azure, incluindo GPUs, habilite/desabilite o acesso SSH e defina as configurações de rede virtual opcionalmente. Você também pode criar uma instância diretamente de blocos de anotações integrados, portal do Azure, modelo do Resource Manager ou SDK do Azure Machine Learning. A cota de núcleos dedicados por região que se aplica à criação da instância de computação é unificada e compartilhada com Azure Machine Learning cota de cluster de computação.
* Atualizar a guia instâncias de computação
* Iniciar, parar e reiniciar uma instância de computação
* Excluir uma instância de computação

Para cada instância de computação em seu espaço de trabalho, você pode:

* Acesse Jupyter, JupyterLab, RStudio VS Code URIs na instância de computação
* SSH na instância de computação. O acesso SSH é desabilitado por padrão, mas pode ser habilitado no momento da criação da instância de computação. O acesso SSH é por meio do mecanismo de chave pública/privada. A guia fornecerá detalhes para a conexão SSH, como endereço IP, nome de usuário e número da porta.
* Obtenha detalhes sobre uma instância de computação específica, como endereço IP e região.

O [RBAC](/azure/role-based-access-control/overview) permite que você controle quais usuários no espaço de trabalho podem criar, excluir, iniciar, parar, reiniciar uma instância de computação. Todos os usuários na função colaborador e proprietário do espaço de trabalho podem criar, excluir, iniciar, parar e reiniciar instâncias de computação no espaço de trabalho. No entanto, somente o criador de uma instância de computação específica tem permissão para acessar Jupyter, JupyterLab e RStudio nessa instância de computação. O criador da instância de computação tem a instância de computação dedicada a elas, tem acesso à raiz e pode acessar o terminal por meio de Jupyter. A instância de computação terá logon de usuário único do usuário criador e todas as ações usarão a identidade desse usuário para o RBAC e a atribuição de execuções de experimento. O acesso SSH é controlado por meio do mecanismo de chave pública/privada.

Você também pode criar uma instância
* Diretamente da experiência de notebooks integrados
* Em portal do Azure
* Do modelo de Azure Resource Manager
* Com Azure Machine Learning SDK

A cota de núcleos dedicados por região, que se aplica à criação da instância de computação é unificada e compartilhada com Azure Machine Learning cota de cluster de treinamento. 

## <a name="compute-target"></a>Destino de Computação

As instâncias de computação podem ser usadas como um [destino de computação de treinamento](concept-compute-target.md#train) semelhante a Azure Machine Learning clusters de treinamento de computação. Provisione uma VM de várias GPUs para executar trabalhos de treinamento distribuídos usando os estimadores TensorFlow/PyTorch. Você também pode criar uma configuração de execução e usá-la para executar seu experimento na instância de computação. Você pode usar a instância de computação como um destino de implantação de inferência local para cenários de teste/depuração.

## <a name="notebookvm"></a>O que aconteceu com a VM do notebook?

As instâncias de computação estão substituindo a VM do notebook.  Em regiões em que as instâncias de computação ainda não estão disponíveis, você pode continuar a usar VMs de notebook com funcionalidade completa e criar novas VMs de notebook.

Todos os arquivos de bloco de anotações armazenados no compartilhamento de arquivos do espaço de trabalho e dados em armazenamentos de dados de espaço de trabalho estarão acessíveis de uma instância de computação No entanto, todos os pacotes personalizados instalados anteriormente em uma VM do notebook precisarão ser instalados novamente na instância de computação. As limitações de cota que se aplicam à criação de clusters de computação também serão aplicadas à criação da instância de computação. 

Em regiões em que as instâncias de computação estão disponíveis, novas VMs de notebook não podem ser criadas. No entanto, você ainda pode acessar e usar VMs de notebook que você criou, com funcionalidade completa. As instâncias de computação podem ser criadas no mesmo espaço de trabalho que as VMs de notebook existentes. 


## <a name="next-steps"></a>Passos seguintes

 * [Tutorial: treinar seu primeiro modelo de ml](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância de computação com um bloco de anotações integrado.
