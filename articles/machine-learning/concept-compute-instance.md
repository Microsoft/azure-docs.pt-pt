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
ms.date: 10/02/2020
ms.openlocfilehash: 68143d3ee5df6dca29c43cb090f5873c4b50060f
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704695"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é uma instância de computação do Azure Machine Learning?

Um caso de computação Azure Machine Learning é uma estação de trabalho gerida baseada em nuvem para cientistas de dados.

As instâncias computacional facilitam o início com o desenvolvimento da Azure Machine Learning, bem como fornecem capacidades de gestão e prontidão empresarial para administradores de TI.  

Use uma instância computacional como o seu ambiente de desenvolvimento totalmente configurado e gerido na nuvem para aprendizagem automática. Também podem ser utilizados como alvo de computação para a formação e inferenidade para fins de desenvolvimento e teste.  

Para a formação de modelos de produção, utilize um [cluster de cálculo Azure Machine Learning](how-to-create-attach-compute-cluster.md) com capacidades de escala de vários nós. Para a implementação do modelo de produção, utilize [o cluster de serviçoS Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Por que usar uma instância computacional?

Um caso compute é uma estação de trabalho totalmente gerida baseada em nuvem otimizada para o seu ambiente de desenvolvimento de aprendizagem automática. Proporciona os seguintes benefícios:

|Principais vantagens|Descrição|
|----|----|
|Produtividade|Pode construir e implementar modelos utilizando cadernos integrados e as seguintes ferramentas no estúdio Azure Machine Learning:<br/>- Jupyter<br/>- JupyterLab<br/>- RStudio (pré-visualização)<br/>A instância compute está totalmente integrada com o espaço de trabalho e estúdio Azure Machine Learning. Pode partilhar cadernos e dados com outros cientistas de dados no espaço de trabalho. Também pode configurar o desenvolvimento remoto do Código VS utilizando [o SSH](how-to-set-up-vs-code-remote.md) |
|Gerido & seguro|Reduza a sua pegada de segurança e adicione o cumprimento dos requisitos de segurança da empresa. As instâncias computacional fornecem políticas de gestão robustas e configurações seguras de rede, tais como:<br/><br/>- Autoprovisionamento a partir de modelos de gestor de recursos ou SDK de aprendizagem automática Azure<br/>- [Controlo de acesso baseado em funções Azure (Azure RBAC)](/azure/role-based-access-control/overview)<br/>- [Suporte de rede virtual](how-to-enable-virtual-network.md#compute-instance)<br/>- Política SSH para permitir/desativar o acesso ao SSH<br/>TLS 1.2 habilitado |
|Pré-configurado &nbsp; para &nbsp; ML|Economize tempo em tarefas de configuração com pacotes ML pré-configurados e atualizados, quadros de aprendizagem profunda, controladores de GPU.|
|Totalmente personalizável|Um amplo suporte para os tipos de VM Azure, incluindo GPUs e personalização de baixo nível, como instalar pacotes e motoristas, torna os cenários avançados uma brisa. |

Pode [criar um caso de computação](how-to-create-manage-compute-instance.md?tabs=python#create) por si mesmo, ou um administrador pode [criar uma instância de computação para si](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Ferramentas e ambientes

> [!IMPORTANT]
> Os itens marcados (pré-visualização) neste artigo estão atualmente em pré-visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O exemplo de computação Azure Machine Learning permite-lhe autorizar, treinar e implementar modelos numa experiência de caderno totalmente integrada no seu espaço de trabalho.

Pode [instalar pacotes](how-to-create-manage-compute-instance.md#install-packages) e [adicionar núcleos](how-to-create-manage-compute-instance.md#add-new-kernels) à sua instância de cálculo.  

Estas ferramentas e ambientes já estão instalados no caso do cálculo: 

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
[SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)</br>de PyPI|Inclui a maioria dos pacotes extras azureml.  Para ver a lista completa, [abra uma janela de terminal na sua instância de computação](how-to-run-jupyter-notebooks.md#terminal) e corra <br/> `conda list -n azureml_py36 azureml*` |
|Outros pacotes PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pacotes Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pacotes de aprendizagem profunda|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pacotes ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & amostras R SDK||

Os pacotes Python estão todos instalados no ambiente **Python 3.6 - AzureML.**  

## <a name="accessing-files"></a>Aceder a ficheiros

Os portátils e scripts R são armazenados na conta de armazenamento predefinido do seu espaço de trabalho na partilha de ficheiros Azure.  Estes ficheiros estão localizados no seu diretório de "ficheiros de utilizador". Este armazenamento facilita a partilha de cadernos entre instâncias computacional. A conta de armazenamento também mantém os seus cadernos conservados com segurança quando para ou apaga uma instância computacional.

A conta de partilha de ficheiros Azure do seu espaço de trabalho é montada como uma unidade na instância computacional. Esta unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio. Isto significa que os cadernos e outros ficheiros que cria no Jupyter, JupyterLab ou RStudio são automaticamente armazenados na partilha de ficheiros e disponíveis para utilização em outras instâncias de computação também.

Os ficheiros na partilha de ficheiros estão acessíveis a partir de todas as instâncias computacional no mesmo espaço de trabalho. Quaisquer alterações a estes ficheiros sobre a instância do cálculo serão seguramente persistiu na parte do ficheiro.

Também pode clonar as mais recentes amostras de Azure Machine Learning para a sua pasta sob o diretório de ficheiros do utilizador na partilha de ficheiros do espaço de trabalho.

Escrever pequenos ficheiros pode ser mais lento nas unidades de rede do que escrever para o próprio disco local de computação.  Se estiver a escrever muitos ficheiros pequenos, tente utilizar um diretório diretamente na instância do cálculo, como um `/tmp` diretório. Note que estes ficheiros não estarão acessíveis a partir de outras instâncias de computação. 

Pode utilizar o `/tmp` diretório na instância de cálculo para os seus dados temporários.  No entanto, não escreva grandes ficheiros de dados no disco oss da instância computacional.  Em vez disso, utilize [as datastores.](concept-azure-machine-learning-architecture.md#datasets-and-datastores) Se instalou a extensão do Git JupyterLab, também pode levar a um abrandamento no desempenho do caso computacional.

## <a name="compute-target"></a>Destino de computação

Os casos de computação podem ser usados como um [alvo de computação de formação](concept-compute-target.md#train) semelhante aos clusters de treinamento de computação Azure Machine Learning. 

Uma instância computacional:
* Tem uma fila de trabalho.
* Gere os postos de trabalho de forma segura num ambiente de rede virtual, sem exigir que as empresas abram o porto SSH. O trabalho executa num ambiente contentorizado e embala as dependências do seu modelo num contentor Docker.
* Pode executar vários pequenos trabalhos em paralelo (pré-visualização).  Dois postos de trabalho por núcleo podem funcionar em paralelo, enquanto os restantes empregos estão em fila.
* Suporta trabalhos de formação distribuídos por vários gPU de nó único

Pode utilizar a instância de cálculo como um alvo local de inferenculação para cenários de teste/depuração.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>O que aconteceu com o Notebook VM?

As instâncias computacional estão a substituir o VM do Caderno.  

Quaisquer ficheiros de portátil armazenados na partilha de ficheiros do espaço de trabalho e dados nas lojas de dados do espaço de trabalho estarão acessíveis a partir de uma instância computacional. No entanto, quaisquer pacotes personalizados previamente instalados num VM de portátil terão de ser reinstalados na instância computacional. As limitações das quotas, que se aplicam à criação de clusters computacional, aplicar-se-ão também à criação de instâncias computacional.

Não é possível criar novos VMs de caderno. No entanto, ainda pode aceder e utilizar VMs de portátil que criou, com plena funcionalidade. Os casos de cálculo podem ser criados no mesmo espaço de trabalho que os VMs de caderno existentes.


## <a name="next-steps"></a>Próximas etapas

* [Criar e gerir uma instância computacional](how-to-create-manage-compute-instance.md)
* [Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.
