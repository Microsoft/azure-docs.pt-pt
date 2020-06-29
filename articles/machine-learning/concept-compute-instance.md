---
title: O que é uma instância de computação do Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Conheça a instância computacional Azure Machine Learning, uma estação de trabalho totalmente gerida com base na nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 8713e7538ae65fc8e750d5b3244482a5503b0e37
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483266"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é uma instância de computação do Azure Machine Learning?

Um caso de computação Azure Machine Learning (pré-visualização) é uma estação de trabalho baseada na nuvem totalmente gerida para cientistas de dados. 

As instâncias computacional facilitam o início com o desenvolvimento da Azure Machine Learning, bem como fornecem capacidades de gestão e prontidão empresarial para administradores de TI.  

Use uma instância computacional como o seu ambiente de desenvolvimento totalmente configurado e gerido na nuvem.

Os casos de computação são normalmente usados como ambientes de desenvolvimento.  Também podem ser usados como alvo de computação para treino e inferencing para desenvolvimento e testes.  Para grandes tarefas, um [cluster de computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com capacidades de escala de vários nós é uma melhor escolha de destino de computação.


## <a name="why-use-a-compute-instance"></a>Por que usar uma instância computacional?

Um caso computacional é uma estação de trabalho totalmente gerida baseada em nuvem otimizada para o seu ambiente de desenvolvimento de aprendizagem automática. Proporciona os seguintes benefícios:

|Principais vantagens||
|----|----|
|Produtividade|Os cientistas de dados podem construir e implementar modelos usando cadernos integrados e as seguintes ferramentas no seu navegador web:<br/>- Jupyter<br/>- JupyterLab<br/>- RStudio (pré-visualização)|
|Gerido & seguro|Reduza a sua pegada de segurança e adicione o cumprimento dos requisitos de segurança da empresa. As instâncias computacional fornecem políticas de gestão robustas e configurações seguras de rede, tais como:<br/><br/>- Fornecimento automático a partir de modelos de gestores de recursos ou SDK de aprendizagem automática Azure<br/>- [Controlo de acesso baseado em funções (RBAC)](/azure/role-based-access-control/overview)<br/>- [Suporte de rede virtual](how-to-enable-virtual-network.md#compute-instance)<br/>- Política SSH para permitir/desativar o acesso ao SSH|
|Pré-configurado &nbsp; ou &nbsp; ML|Economize tempo em tarefas de configuração com pacotes ML pré-configurados e atualizados, quadros de aprendizagem profunda, controladores de GPU.|
|Totalmente personalizável|Um amplo suporte para os tipos de VM Azure, incluindo GPUs e personalização de baixo nível, como instalar pacotes e motoristas, torna os cenários avançados uma brisa. |

## <a name="tools-and-environments"></a><a name="contents"></a>Ferramentas e ambientes

O exemplo de computação Azure Machine Learning permite-lhe autorizar, treinar e implementar modelos numa experiência de caderno totalmente integrada no seu espaço de trabalho.


Estas ferramentas e ambientes são instalados no caso do cálculo: 

|Ferramentas gerais & ambientes|Detalhes|
|----|:----:|
|Controladores|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Biblioteca Intel MPI||
|CLI do Azure ||
|Amostras de aprendizagem automática Azure ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**Ferramentas R** & ambientes|Detalhes|
|----|:----:|
|Edição Open Source do RStudio Server (pré-visualização)||
|Núcleo R||
|Azure Machine Learning SDK para R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK samples (Amostras do SDK)|

|**Ferramentas PYTHON** & ambientes|Detalhes|
|----|----|
|Anaconda Python||
|Jupyter e extensões||
|Jupyterlab e extensões||
[SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>de PyPI|Inclui a maioria dos pacotes extras azureml.  Para ver a lista completa, [abra uma janela de terminal na sua instância de computação](how-to-run-jupyter-notebooks.md#terminal) e corra <br/> `conda list -n azureml_py36 azureml*` |
|Outros pacotes PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacotes Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacotes de aprendizagem profunda|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacotes ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & amostras R SDK||

Os pacotes Python estão todos instalados no ambiente **Python 3.6 - AzureML.**  

Os casos de computação são normalmente usados como ambientes de desenvolvimento.  Também podem ser usados como alvo de computação para treino e inferencing para desenvolvimento e testes.  Para grandes tarefas, um [cluster de computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com capacidades de escala de vários nós é uma melhor escolha de destino de computação.

### <a name="installing-packages"></a>Instalar os pacotes

Pode instalar pacotes diretamente num bloco de notas jupyter ou Rstudio:

* RStudio Utilize o **separador Pacotes** no canto inferior direito ou o **separador Consola** na parte superior esquerda.  
* Python: Adicione código de instalação e execute numa célula de caderno Jupyter.

Ou pode aceder a uma janela terminal de qualquer uma destas formas:

* RStudio: Selecione o **separador Terminal** em cima à esquerda.
* Jupyter Lab: Selecione o **azulejo terminal** sob o **outro** título no separador Launcher.
* Jupyter: Selecione **Novo Terminal de>** no topo direito no separador Ficheiros.
* SSH para a máquina.  Em seguida, instale pacotes Python no ambiente **Python 3.6 - AzureML.**  Instale as embalagens R no ambiente **R.**

## <a name="accessing-files"></a>Aceder a ficheiros

Os portátils e scripts R são armazenados na conta de armazenamento predefinido do seu espaço de trabalho na partilha de ficheiros Azure.  Estes ficheiros estão localizados no seu diretório de "ficheiros de utilizador". Este armazenamento facilita a partilha de cadernos entre instâncias computacional. A conta de armazenamento também mantém os seus cadernos conservados com segurança quando para ou apaga uma instância computacional.

A conta de partilha de ficheiros Azure do seu espaço de trabalho é montada como uma unidade na instância computacional. Esta unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio.

Os ficheiros na partilha de ficheiros estão acessíveis a partir de todas as instâncias computacional no mesmo espaço de trabalho. Quaisquer alterações a estes ficheiros sobre a instância do cálculo serão seguramente persistiu na parte do ficheiro.

Também pode clonar as mais recentes amostras de Azure Machine Learning para a sua pasta sob o diretório de ficheiros do utilizador na partilha de ficheiros do espaço de trabalho.

Escrever pequenos ficheiros pode ser mais lento nas unidades de rede do que escrever para o próprio VM.  Se estiver a escrever muitos ficheiros pequenos, tente utilizar um diretório diretamente na instância do cálculo, como um `/tmp` diretório. Por favor, note que estes ficheiros não estarão acessíveis a partir de outras instâncias de computação no espaço de trabalho.

## <a name="managing-a-compute-instance"></a>Gerir uma instância computacional

No seu espaço de trabalho no estúdio Azure Machine Learning, selecione **Compute**e **selecione Compute Instance** no topo.

![Gerir uma instância computacional](./media/concept-compute-instance/manage-compute-instance.png)

Pode realizar as seguintes ações:

* Criar uma instância de computação. Especifique o nome, tipo Azure VM incluindo GPUs (por favor note que o tipo VM não pode ser alterado após a criação), ativar/desativar o acesso SSH e configurar opcionalmente as definições de rede virtual. Também pode criar um caso diretamente a partir de cadernos integrados, portal Azure, modelo de Gestor de Recursos ou SDK de Aprendizagem de Máquinas Azure. Os núcleos dedicados por quota de região que se aplica à criação de instâncias computacional são unificados e partilhados com a quota de cluster de cálculo Azure Machine Learning.
* Atualizar o separador de instâncias computacional
* Iniciar, parar e reiniciar uma instância de computação. Pare o VM quando não estiver a usá-lo para reduzir o custo. Então, comece de novo quando precisar.
* Excluir uma instância computacional

Para cada instância de cálculo no seu espaço de trabalho pode:

* Acesso Jupyter, JupyterLab, RStudio na instância compute compute
* SSH em caso de computação. O acesso ao SSH é desativado por padrão, mas pode ser ativado no tempo de criação de instâncias de computação. O acesso ao SSH é através de um mecanismo chave público/privado. O separador irá dar-lhe detalhes para a ligação SSH, como endereço IP, nome de utilizador e número de porta.
* Obtenha detalhes sobre uma instância computacional específica, como endereço IP e região.

[O RBAC](/azure/role-based-access-control/overview) permite-lhe controlar quais os utilizadores no espaço de trabalho que podem criar, eliminar, iniciar, parar, reiniciar uma instância de computação. Todos os utilizadores no espaço de trabalho colaborador e papel de proprietário podem criar, eliminar, iniciar, parar e reiniciar casos de cálculo em todo o espaço de trabalho. No entanto, apenas o criador de uma instância computacional específica é permitido aceder a Jupyter, JupyterLab e RStudio nessa instância de computação. O criador da instância computacional tem a instância computacional dedicada a eles, tem acesso à raiz, e pode terminal através de Jupyter. A instância computacional terá um login de utilizador único do utilizador criador e todas as ações usarão a identidade desse utilizador para o RBAC e a atribuição de corridas de experiências. O acesso ao SSH é controlado através de um mecanismo chave público/privado.

Também pode criar um caso
* Diretamente da experiência de cadernos integrados
* No portal Azure
* Do modelo de gestor de recursos Azure
* Com Azure Machine Learning SDK

Os núcleos dedicados por quota de região, que se aplica à criação de instâncias computacional é unificado e partilhado com a quota de cluster de formação Azure Machine Learning. 

## <a name="compute-target"></a>Destino de Computação

Os casos de computação podem ser usados como um [alvo de computação de formação](concept-compute-target.md#train) semelhante aos clusters de treinamento de computação Azure Machine Learning. Provisão de um VM multi-GPU para executar trabalhos de formação distribuídos utilizando estimadores tensorFlow/PyTorch. Também pode criar uma configuração de execução e usá-la para executar a sua experiência em instância de computação. Pode utilizar a instância de cálculo como um alvo local de inferenculação para cenários de teste/depuração.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>O que aconteceu com o Notebook VM?

As instâncias computacional estão a substituir o VM do Caderno.  

Quaisquer ficheiros de portátil armazenados na partilha de ficheiros do espaço de trabalho e dados nas lojas de dados do espaço de trabalho estarão acessíveis a partir de uma instância computacional. No entanto, quaisquer pacotes personalizados previamente instalados num VM de portátil terão de ser reinstalados na instância computacional. As limitações das quotas aplicáveis à criação de clusters computacional aplicar-se-ão também à criação de instâncias computacional. 

Não é possível criar novos VMs de caderno. No entanto, ainda pode aceder e utilizar VMs de portátil que criou, com plena funcionalidade. Os casos de cálculo podem ser criados no mesmo espaço de trabalho que os VMs de caderno existentes. 


## <a name="next-steps"></a>Passos seguintes

 * [Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.
