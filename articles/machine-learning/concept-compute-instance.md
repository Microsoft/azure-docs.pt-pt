---
title: O que é um exemplo de computação de aprendizagem automática Azure?
titleSuffix: Azure Machine Learning
description: Conheça o caso do computacional Azure Machine Learning, uma estação de trabalho totalmente gerida em nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283930"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é um exemplo de computação de aprendizagem automática Azure?

Um exemplo de computação de Machine Learning Azure (pré-visualização) é uma estação de trabalho totalmente gerida em nuvem para cientistas de dados. 

Os casos computacionais facilitam o início com o desenvolvimento do Azure Machine Learning, bem como fornecem capacidades de gestão e prontidão empresarial para administradores de TI.  

Use um exemplo de cálculo como o seu ambiente de desenvolvimento totalmente configurado e gerido na nuvem.

Os casos de computação são normalmente usados como ambientes de desenvolvimento.  Também podem ser utilizados como um alvo computacional para a formação e inferência para o desenvolvimento e teste.  Para grandes tarefas, um cluster de [computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com capacidades de escala de vários nós é uma melhor escolha de alvo computacional.


## <a name="why-use-a-compute-instance"></a>Por que usar um exemplo de computação?

Um exemplo de computação é uma estação de trabalho totalmente gerida em nuvem otimizada para o seu ambiente de desenvolvimento de aprendizagem automática. Proporciona os seguintes benefícios:

|Principais vantagens||
|----|----|
|Produtividade|Os cientistas de dados podem construir e implementar modelos usando cadernos integrados e as seguintes ferramentas no seu navegador web:<br/>- Jupyter<br/>- JupyterLab<br/>- RStudio|
|Gerido & seguro|Reduza a sua pegada de segurança e adicione o cumprimento dos requisitos de segurança da empresa. As instâncias computadas fornecem políticas de gestão robustas e configurações seguras de networking tais como:<br/><br/>- Fornecimento automático a partir de modelos de Gestor de Recursos ou SDK de Aprendizagem automática Azure<br/>- [Controlo de acesso baseado em funções (RBAC)](/azure/role-based-access-control/overview)<br/>- [Suporte de rede virtual](how-to-enable-virtual-network.md#compute-instance)<br/>- Política sSH para permitir/desativar o acesso ao SSH|
|Reconfigurado&nbsp;&nbsp;ou ML|Poupe tempo em tarefas de configuração com pacotes ML pré-configurados e atualizados, quadros de aprendizagem profunda, condutores de GPU.|
|Totalmente personalizável|Um amplo suporte para tipos de VM Azure, incluindo GPUs e uma personalização de baixo nível, como a instalação de pacotes e condutores, torna os cenários avançados uma brisa. |

## <a name="tools-and-environments"></a><a name="contents"></a>Ferramentas e ambientes

A instância computacional Azure Machine Learning permite-lhe autor, treinar e implementar modelos numa experiência de caderno totalmente integrada no seu espaço de trabalho.


Estas ferramentas e ambientes são instalados na instância computacional: 

|Ferramentas gerais & ambientes|Detalhes|
|----|:----:|
|Controladores|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Biblioteca Intel MPI||
|CLI do Azure ||
|Amostras de aprendizagem automática azure ||
|Motor EDAT de aprendizagem automática azure ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**Ferramentas R** & ambientes|Detalhes|
|----|:----:|
|RStudio Server Open Source Edition||
|Núcleo R||
|SDK de aprendizagem automática azure para R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Exemplos de SDK|

|**Ferramentas PYTHON** & ambientes|Detalhes|
|----|----|
|Anaconda Python||
|Jupyter e extensões||
|Jupyterlab e extensões||
|Visual Studio Code ||
[SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>da PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Outros pacotes PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacotes de condomínio|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacotes de aprendizagem profunda|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacotes ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Pitão de aprendizagem de máquinas azure & amostras R SDK||

Os pacotes Python estão todos instalados no ambiente **Python 3.6 - AzureML.**  

Os casos de computação são normalmente usados como ambientes de desenvolvimento.  Também podem ser utilizados como um alvo computacional para a formação e inferência para o desenvolvimento e teste.  Para grandes tarefas, um cluster de [computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com capacidades de escala de vários nós é uma melhor escolha de alvo computacional.

### <a name="installing-packages"></a>Instalar os pacotes

Pode instalar pacotes diretamente num caderno Jupyter ou Rstudio:

* RStudio Utilize o separador **Pacotes** na parte inferior direita ou o separador **Consola** na parte superior esquerda.  
* Python: Adicione o código de instalação e execute numa célula de caderno Jupyter.

Ou pode aceder a uma janela terminal de qualquer uma destas formas:

* RStudio: Selecione o separador **Terminal** em cima à esquerda.
* Jupyter Lab: Selecione o azulejo **terminal** sob a **outra** rubrica no separador Launcher.
* Jupyter: Selecione **New>Terminal** em cima direito no separador Ficheiros.
* SSH para a máquina.  Em seguida, instale pacotes Python no ambiente **Python 3.6 - AzureML.**  Instale pacotes R no ambiente **R.**

## <a name="accessing-files"></a>Aceder a ficheiros

Os cadernos e scripts R são armazenados na conta de armazenamento padrão do seu espaço de trabalho na partilha de ficheiros Azure.  Estes ficheiros estão localizados no âmbito do seu diretório "Ficheiros de Utilizador". Este armazenamento facilita a partilha de cadernos entre instâncias de cálculo. A conta de armazenamento também mantém os seus cadernos preservados de forma segura quando para ou elimina uma instância de cálculo.

A conta de partilha de ficheiros Azure do seu espaço de trabalho é montada como uma unidade na instância computacional. Esta unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio.

Os ficheiros na partilha de ficheiros são acessíveis a partir de todos os casos de computação no mesmo espaço de trabalho. Quaisquer alterações a estes ficheiros na instância computacional serão continuadas de forma fiável à parte do ficheiro.

Também pode clonar as mais recentes amostras de Machine Learning Azure para a sua pasta sob o diretório de ficheiros do utilizador na partilha de ficheiros do espaço de trabalho.

Escrever pequenos ficheiros pode ser mais lento nas unidades de rede do que escrever para o próprio VM.  Se estiver a escrever muitos ficheiros pequenos, tente utilizar um diretório `/tmp` diretamente na instância computacional, como um diretório. Por favor, note que estes ficheiros não serão acessíveis a partir de outros casos de computação no espaço de trabalho.

## <a name="managing-a-compute-instance"></a>Gerir um caso de cálculo

No seu espaço de trabalho no estúdio Azure Machine Learning, **selecione Compute**e, em seguida, selecione **Compute Instance** no topo.

![Gerir uma instância de cálculo](./media/concept-compute-instance/manage-compute-instance.png)

Pode realizar as seguintes ações:

* Criar uma instância de computação. Especifique o nome, tipo Azure VM, incluindo GPUs (por favor note que o tipo VM não pode ser alterado após a criação), ativar/desativar o acesso sSH e configurar opcionalmente as definições de rede virtual. Também pode criar uma instância diretamente a partir de cadernos integrados, portal Azure, modelo de Gestor de Recursos ou SDK de Aprendizagem automática Azure. Os núcleos dedicados por quota de região que se aplica à criação de instâncias computacionais são unificados e partilhados com a quota de cluster de computação Azure Machine Learning.
* Atualizar o separador de instâncias computadas
* Iniciar, parar e reiniciar uma instância de computação
* Eliminar uma instância de cálculo

Para cada instância de cálculo no seu espaço de trabalho pode:

* Access Jupyter, JupyterLab, RStudio na instância computacional
* SSH em instância de computação. O acesso SSH é desativado por padrão, mas pode ser ativado no tempo de criação de instâncias computacionais. O acesso sSH é através de mecanismo-chave público/privado. O separador irá dar-lhe detalhes sobre a ligação SSH, tais como endereço IP, nome de utilizador e número de porta.
* Obtenha detalhes sobre um caso de cálculo específico, como endereço IP, e região.

[O RBAC](/azure/role-based-access-control/overview) permite controlar quais os utilizadores do espaço de trabalho que podem criar, eliminar, iniciar, parar, reiniciar uma instância de cálculo. Todos os utilizadores no espaço de trabalho e na função do proprietário podem criar, eliminar, iniciar, parar e reiniciar instâncias computacionais em todo o espaço de trabalho. No entanto, apenas o criador de uma instância de cálculo específica é permitido aceder a Jupyter, JupyterLab e RStudio nessa instância de cálculo. O criador da instância computacional tem a instância computacional dedicada a eles, tem acesso raiz, e pode terminal entrar através de Jupyter. A instância computacional terá um único utilizador de login do utilizador criador e todas as ações usarão a identidade desse utilizador para RBAC e atribuição de executações de experiências. O acesso sSH é controlado através de mecanismo de chave público/privado.

Também pode criar uma instância
* Diretamente da experiência de cadernos integrados
* No portal Azure
* Do modelo de Gestor de Recursos Azure
* Com SDK de aprendizagem automática azure

Os núcleos dedicados por quota de região, que se aplica à criação de instâncias computacionais, são unificados e partilhados com a quota de cluster de formação azure machine learning. 

## <a name="compute-target"></a>Destino de Computação

Os casos computacionais podem ser usados como um [alvo computacional](concept-compute-target.md#train) de formação semelhante aos clusters de formação de computação Azure Machine Learning. Providenciar um VM multi-GPU para executar trabalhos de formação distribuídos utilizando estimativas TensorFlow/PyTorch. Também pode criar uma configuração de execução e usá-la para executar a sua experiência em instância de cálculo. Pode utilizar a instância computacional como um alvo de implementação local de inferência para cenários de teste/depuração.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>O que aconteceu com o Notebook VM?

Os casos de computação estão a substituir o VM do Caderno.  

Quaisquer ficheiros portátil armazenados na partilha de ficheiros do espaço de trabalho e dados nas lojas de dados do espaço de trabalho estarão acessíveis a partir de uma instância de cálculo. No entanto, quaisquer pacotes personalizados previamente instalados num VM de caderno terão de ser reinstalados na instância do cálculo. As limitações de quota aplicáveis à criação de clusters computacionais aplicar-se-ão também à criação de instâncias computadas. 

Não podem ser criados novos VMs portátil. No entanto, ainda pode aceder e utilizar VMs portátil que criou, com funcionalidade completa. Os casos de computação podem ser criados no mesmo espaço de trabalho que os VMs de caderno existentes. 


## <a name="next-steps"></a>Passos seguintes

 * [Tutorial: Treinar o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.
