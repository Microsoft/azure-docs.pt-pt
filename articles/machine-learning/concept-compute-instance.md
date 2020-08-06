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
ms.date: 07/27/2020
ms.openlocfilehash: f4938d517d9a5c244045798a79f31b96bacd03f5
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829446"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>O que é uma instância de computação do Azure Machine Learning?

Um caso de computação Azure Machine Learning é uma estação de trabalho gerida baseada em nuvem para cientistas de dados.

As instâncias computacional facilitam o início com o desenvolvimento da Azure Machine Learning, bem como fornecem capacidades de gestão e prontidão empresarial para administradores de TI.  

Use uma instância computacional como o seu ambiente de desenvolvimento totalmente configurado e gerido na nuvem para aprendizagem automática. Também podem ser utilizados como alvo de computação para a formação e inferenidade para fins de desenvolvimento e teste.  

Para a formação de modelos de produção, utilize um [cluster de cálculo Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com capacidades de escala de vários nós. Para a implementação do modelo de produção, utilize [o cluster de serviçoS Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Por que usar uma instância computacional?

Um caso computacional é uma estação de trabalho totalmente gerida baseada em nuvem otimizada para o seu ambiente de desenvolvimento de aprendizagem automática. Proporciona os seguintes benefícios:

|Principais vantagens|Descrição|
|----|----|
|Produtividade|Pode construir e implementar modelos utilizando cadernos integrados e as seguintes ferramentas no estúdio Azure Machine Learning:<br/>- Jupyter<br/>- JupyterLab<br/>- RStudio (pré-visualização)<br/>A instância compute está totalmente integrada com o espaço de trabalho e estúdio Azure Machine Learning. Pode partilhar cadernos e dados com outros cientistas de dados no espaço de trabalho. Também pode configurar o desenvolvimento remoto do Código VS utilizando [o SSH](how-to-set-up-vs-code-remote.md) |
|Gerido & seguro|Reduza a sua pegada de segurança e adicione o cumprimento dos requisitos de segurança da empresa. As instâncias computacional fornecem políticas de gestão robustas e configurações seguras de rede, tais como:<br/><br/>- Fornecimento automático a partir de modelos de gestores de recursos ou SDK de aprendizagem automática Azure<br/>- [Controlo de acesso baseado em funções Azure (Azure RBAC)](/azure/role-based-access-control/overview)<br/>- [Suporte de rede virtual](how-to-enable-virtual-network.md#compute-instance)<br/>- Política SSH para permitir/desativar o acesso ao SSH<br/>TLS 1.2 habilitado |
|Pré-configurado &nbsp; para &nbsp; ML|Economize tempo em tarefas de configuração com pacotes ML pré-configurados e atualizados, quadros de aprendizagem profunda, controladores de GPU.|
|Totalmente personalizável|Um amplo suporte para os tipos de VM Azure, incluindo GPUs e personalização de baixo nível, como instalar pacotes e motoristas, torna os cenários avançados uma brisa. |

## <a name="tools-and-environments"></a><a name="contents"></a>Ferramentas e ambientes

> [!IMPORTANT]
> As ferramentas marcadas (pré-visualização) abaixo estão atualmente em visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

A conta de partilha de ficheiros Azure do seu espaço de trabalho é montada como uma unidade na instância computacional. Esta unidade é o diretório de trabalho padrão para Jupyter, Jupyter Labs e RStudio. Isto significa que os cadernos e outros ficheiros que cria no Jupyter, JupyterLab ou RStudio são automaticamente armazenados na partilha de ficheiros e disponíveis para utilização em outras instâncias de computação também.

Os ficheiros na partilha de ficheiros estão acessíveis a partir de todas as instâncias computacional no mesmo espaço de trabalho. Quaisquer alterações a estes ficheiros sobre a instância do cálculo serão seguramente persistiu na parte do ficheiro.

Também pode clonar as mais recentes amostras de Azure Machine Learning para a sua pasta sob o diretório de ficheiros do utilizador na partilha de ficheiros do espaço de trabalho.

Escrever pequenos ficheiros pode ser mais lento nas unidades de rede do que escrever para o próprio disco local de computação.  Se estiver a escrever muitos ficheiros pequenos, tente utilizar um diretório diretamente na instância do cálculo, como um `/tmp` diretório. Por favor, note que estes ficheiros não estarão acessíveis a partir de outras instâncias de computação. 

Pode utilizar o `/tmp` diretório na instância de cálculo para os seus dados temporários.  No entanto, não escreva grandes ficheiros de dados no disco oss da instância computacional.  Em vez disso, utilize [as datastores.](concept-azure-machine-learning-architecture.md#datasets-and-datastores) Se instalou a extensão do Git JupyterLab, também pode levar a um abrandamento no desempenho do caso computacional.

## <a name="managing-a-compute-instance"></a>Gerir uma instância computacional

No seu espaço de trabalho no estúdio Azure Machine Learning, selecione **Compute**e **selecione Compute Instance** no topo.

![Gerir uma instância computacional](./media/concept-compute-instance/manage-compute-instance.png)

Pode realizar as seguintes ações:

* [Criar uma instância computacional](#create). 
* Refresque o separador de instâncias computacional.
* Iniciar, parar e reiniciar uma instância de computação.  Pagas pelo exemplo sempre que está a funcionar. Pare a instância de cálculo quando não estiver a usá-lo para reduzir o custo. Parar um caso de computação o impede. Então, comece de novo quando precisar. 
* Apagar uma instância computacional.
* Filtre a lista de casos de computação para as que criou.  Estas são as instâncias de computação a que pode aceder.

Para cada instância de cálculo no seu espaço de trabalho a que tem acesso, pode:

* Acesso Jupyter, JupyterLab, RStudio na instância compute compute
* SSH em caso de computação. O acesso ao SSH é desativado por padrão, mas pode ser ativado no tempo de criação de instâncias de computação. O acesso ao SSH é através de um mecanismo chave público/privado. O separador irá dar-lhe detalhes para a ligação SSH, como endereço IP, nome de utilizador e número de porta.
* Obtenha detalhes sobre uma instância computacional específica, como endereço IP e região.

[O RBAC](/azure/role-based-access-control/overview) permite-lhe controlar quais os utilizadores no espaço de trabalho que podem criar, eliminar, iniciar, parar, reiniciar uma instância de computação. Todos os utilizadores no espaço de trabalho colaborador e papel de proprietário podem criar, eliminar, iniciar, parar e reiniciar casos de cálculo em todo o espaço de trabalho. No entanto, apenas o criador de uma instância computacional específica é permitido aceder a Jupyter, JupyterLab e RStudio nessa instância de computação. O criador da instância computacional tem a instância computacional dedicada a eles, tem acesso à raiz, e pode terminal através do Jupyter/JupyterLab/RStudio. A instância computacional terá um login de utilizador único do utilizador criador e todas as ações usarão a identidade desse utilizador para o RBAC e a atribuição de corridas de experiências. O acesso ao SSH é controlado através de um mecanismo chave público/privado.

Estas ações podem ser controladas pelo RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

### <a name="create-a-compute-instance"></a><a name="create"></a>Criar uma instância computacional

No seu espaço de trabalho no estúdio Azure Machine Learning, crie uma nova instância de computação a partir da secção **Compute** ou na secção **Notebooks** quando estiver pronto para executar um dos seus cadernos.

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Criar uma nova instância computacional":::


|Campo  |Descrição  |
|---------|---------|
|Nome computacional     |  <li>O nome é necessário e deve ter entre 3 a 24 caracteres de comprimento.</li><li>Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e o **-** personagem.</li><li>O nome deve começar com uma carta</li><li>O nome tem de ser único em todos os cálculos existentes dentro de uma região de Azure. Verá um alerta se o nome que escolher não for único</li><li>Se **-** o caráter é usado, então precisa ser seguido por pelo menos uma letra mais tarde no nome</li>     |
|Tipo de máquina virtual |  Escolha CPU ou GPU. Este tipo não pode ser alterado após a criação     |
|Tamanho da máquina virtual     |  Os tamanhos de máquina virtual suportados podem ser restringidos na sua região. Consulte a [lista de disponibilidades](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Ativar/desativar o acesso ao SSH     |   O acesso ao SSH é desativado por predefinição.  O acesso ao SSH não pode ser. mudou após a criação. Certifique-se de que permite o acesso se planeia depurar interativamente com [o vs Code Remote](how-to-set-up-vs-code-remote.md)   |
|Definições avançadas     |  Opcional. Configurar uma rede virtual. Especifique o **grupo de Recursos**, rede **virtual**e **sub-rede** para criar a instância computacional dentro de uma Rede Virtual Azure (vnet). Para mais informações, consulte estes [requisitos de rede](how-to-enable-virtual-network.md#compute-instance) para vnet .        |

Também pode criar um caso
* Diretamente da experiência de [cadernos integrados](tutorial-1st-experiment-sdk-setup.md#azure)
* No portal Azure
* Do modelo do Gestor de Recursos Azure. Para um modelo de exemplo, consulte o [modelo de instância de cálculo Azure Machine Learning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Com [Azure Machine Learning SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb)
* Da [extensão do CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Os núcleos dedicados por região por quota familiar VM e quotas regionais totais, que se aplica à criação de instâncias computacional. é unificada e partilhada com a quota de cluster de computação de aprendizagem de máquinas Azure. Parar a instância de computação não liberta quota para garantir que poderá reiniciar a instância do cálculo.

## <a name="compute-target"></a>Destino de computação

Os casos de computação podem ser usados como um [alvo de computação de formação](concept-compute-target.md#train) semelhante aos clusters de treinamento de computação Azure Machine Learning. 

Uma instância computacional:
* Tem uma fila de trabalho.
* Gere os postos de trabalho de forma segura num ambiente de rede virtual, sem exigir que as empresas abram o porto SSH. O trabalho executa num ambiente contentorizado e embala as dependências do seu modelo num contentor Docker.
* Pode executar vários pequenos trabalhos em paralelo (pré-visualização).  Dois postos de trabalho por núcleo podem funcionar em paralelo, enquanto os restantes empregos estão em fila.

Pode utilizar a instância de cálculo como um alvo local de inferenculação para cenários de teste/depuração.

> [!NOTE]
> Os trabalhos de formação distribuídos não são apoiados em casos de computação.  Utilização (clusters de computação](como configurar-se-alvos de treino.md#amlcompute) para formação distribuída.

Para mais detalhes, consulte o portátil [train-on-computeinstance](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb). Este caderno também está disponível na pasta **Amostras** do estúdio em *treino/treino-on-computeinstance*.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>O que aconteceu com o Notebook VM?

As instâncias computacional estão a substituir o VM do Caderno.  

Quaisquer ficheiros de portátil armazenados na partilha de ficheiros do espaço de trabalho e dados nas lojas de dados do espaço de trabalho estarão acessíveis a partir de uma instância computacional. No entanto, quaisquer pacotes personalizados previamente instalados num VM de portátil terão de ser reinstalados na instância computacional. As limitações das quotas aplicáveis à criação de clusters computacional aplicar-se-ão também à criação de instâncias computacional.

Não é possível criar novos VMs de caderno. No entanto, ainda pode aceder e utilizar VMs de portátil que criou, com plena funcionalidade. Os casos de cálculo podem ser criados no mesmo espaço de trabalho que os VMs de caderno existentes.


## <a name="next-steps"></a>Passos seguintes

 * [Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.
