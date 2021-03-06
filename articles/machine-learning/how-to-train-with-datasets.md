---
title: Treine com conjuntos de dados de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Saiba como disponibilizar os seus dados ao seu computação local ou remoto para formação de modelos com conjuntos de dados Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 0b2bb49863e07e6f06512e868ed12ecf00cc11c2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872394"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Modelos de trem com conjuntos de dados de aprendizagem de máquinas Azure 

Neste artigo, aprende-se a trabalhar com [conjuntos de dados Azure Machine Learning](/python/api/azureml-core/azureml.core.dataset%28class%29) para treinar modelos de aprendizagem automática.  Pode utilizar conjuntos de dados no seu alvo de computação local ou remota sem se preocupar com cordas de ligação ou caminhos de dados. 

Os conjuntos de dados Azure Machine Learning proporcionam uma integração perfeita com a funcionalidade de formação de aprendizagem automática Azure como [scriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig) [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive)e [Azure Machine Learning pipelines](./how-to-create-machine-learning-pipelines.md).

Se não estiver pronto para disponibilizar os seus dados para a formação de modelos, mas pretender carregar os seus dados no seu caderno para exploração de dados, consulte como [explorar os dados no seu conjunto de dados.](how-to-create-register-datasets.md#explore-data) 

## <a name="prerequisites"></a>Pré-requisitos

Para criar e treinar com conjuntos de dados, precisa:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um [espaço de trabalho de aprendizagem automática Azure.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado](/python/api/overview/azure/ml/install) (>= 1.13.0), que inclui o `azureml-datasets` pacote.

> [!Note]
> Algumas classes dataset têm dependências do pacote [azureml-dataprep.](https://pypi.org/project/azureml-dataprep/) Para os utilizadores do Linux, estas classes são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Consumir conjuntos de dados em scripts de treinamento de machine learning

Se tiver dados estruturados ainda não registados como conjunto de dados, crie um Separadors e utilize-os diretamente no seu script de treino para a sua experiência local ou remota.

Neste exemplo, cria-se um [Separadornista](/python/api/azureml-core/azureml.data.tabulardataset) Não Registado e especifica-o como argumento de script no objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) para treino. Se pretender reutilizar este SeparadorDataset com outras experiências no seu espaço de trabalho, consulte [como registar conjuntos de dados no seu espaço de trabalho.](how-to-create-register-datasets.md#register-datasets)

### <a name="create-a-tabulardataset"></a>Criar um Conjunto de Dados Tabular

O código a seguir cria um Separador Não Registado a partir de um url web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Os objetos TabularDataset fornecem a capacidade de carregar os dados no seu SeparadorDataset num pandas ou Nodilha de Dados de Faíscas para que possa trabalhar com bibliotecas familiares de preparação e formação de dados sem ter de deixar o seu caderno.

### <a name="access-dataset-in-training-script"></a>Conjunto de dados de acesso no script de formação

O código que se segue configura um argumento de script `--input-data` que irá especificar quando configurar a sua formação (ver secção seguinte). Quando o conjunto de dados tabular é passado como o valor do argumento, o Azure ML resolverá isso para iD do conjunto de dados, que pode então utilizar para aceder ao conjunto de dados no seu script de treino (sem ter de codificar o nome ou ID do conjunto de dados no seu script). Em seguida, utiliza o [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) método para carregar esse conjunto de dados num dataframe de pandas para mais exploração e preparação de dados antes do treino.

> [!Note]
> Se a sua fonte de dados original contiver NaN, cordas vazias ou valores em branco, quando `to_pandas_dataframe()` utilizar, esses valores são substituídos como um valor *nulo.*

Se precisar de carregar os dados preparados num novo conjunto de dados a partir de um dataframe de pandas na memória, escreva os dados para um ficheiro local, como um parquet, e crie um novo conjunto de dados a partir desse ficheiro. Saiba mais sobre [como criar conjuntos de dados.](how-to-create-register-datasets.md)

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Configure a corrida de treino

Um objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) é usado para configurar e submeter a execução de treino.

Este código cria um objeto ScriptRunConfig, `src` que especifica

* Um diretório de guiões para os seus scripts. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução.
* O guião de treino, *train_titanic.py.*
* O conjunto de dados de entrada para o treino, `titanic_ds` como um argumento de script. O Azure ML resolverá isto com o iD correspondente do conjunto de dados quando este for passado para o seu script.
* O alvo do cálculo para a corrida.
* O ambiente para a corrida.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Monte ficheiros para alvos de computação remota

Se tiver dados não estruturados, crie um [Dataset de Ficheiros](/python/api/azureml-core/azureml.data.filedataset) e monte ou descarregue os seus ficheiros de dados para os disponibilizar ao seu alvo de computação remota para treino. Saiba quando usar [o mount vs. download](#mount-vs-download) para as suas experiências de treino remoto. 

O exemplo seguinte, 

* Cria um Dataset de ficheiro de entrada, `mnist_ds` para os seus dados de treino.
* Especifica onde escrever resultados de formação e promover esses resultados como data de arquivo.
* Monta o conjunto de dados de entrada no alvo do cálculo.

> [!Note]
> Se estiver a utilizar uma imagem base personalizada do Docker, terá de instalar o fusível através `apt-get install -y fuse` de uma dependência para o conjunto de dados funcionar. Aprenda a [construir uma imagem de construção personalizada.](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)

Para o exemplo do caderno, consulte [Como configurar uma formação com entrada e saída de dados](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

### <a name="create-a-filedataset"></a>Criar um Conjunto de Dados de Ficheiros

O exemplo a seguir cria um Dataset de Ficheiros não registado, `mnist_data` a partir de urls web. Este Dataset de ficheiros é os dados de entrada para a sua execução de treino.

Saiba mais sobre [como criar conjuntos de dados a](how-to-create-register-datasets.md) partir de outras fontes.

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>Onde escrever a produção de treino

Pode especificar onde escrever os seus resultados de formação com um [objeto OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). 

Saída FicheiroDatasetConfig permite-lhe: 

* Monte ou carrefique a saída de uma corrida para o armazenamento na nuvem que especifica.
* Guarde a saída como ficheiroDataset para estes tipos de armazenamento suportados:
    * Bolha de Azure
    * Partilha de ficheiros do Azure
    * Azure Data Lake Storage gerações 1 e 2
* Acompanhe a linhagem de dados entre as corridas de treino.

O código que se segue especifica que os resultados da formação devem ser guardados como data-data de ficheiro `outputdataset` na pasta na loja de dados blob predefinido, `def_blob_store` . 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>Configure a corrida de treino

Recomendamos a passagem do conjunto de dados como argumento ao montar através `arguments` do parâmetro do `ScriptRunConfig` construtor. Ao fazê-lo, obtém-se o caminho de dados (ponto de montagem) no seu roteiro de treino através de argumentos. Desta forma, você pode usar o mesmo script de treino para depurar local e treino remoto em qualquer plataforma de nuvem.

O exemplo a seguir cria um ScriptRunConfig que passa no Dataset de Ficheiros via `arguments` . Após a apresentação da execução, os ficheiros de dados referidos pelo conjunto de dados `mnist_ds` são montados no alvo do cálculo e os resultados de formação são guardados na pasta especificada `outputdataset` na datastore predefinida.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Roteiro de treino simples

O seguinte script é submetido através do ScriptRunConfig. Lê o `mnist_ds ` conjunto de dados como entrada e escreve o ficheiro para a `outputdataset` pasta na loja de dados blob predefinido, `def_blob_store` .

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Monte vs download

A montagem ou descarregamento de ficheiros de qualquer formato são suportados para conjuntos de dados criados a partir do armazenamento de Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL. 

Quando **monta** um conjunto de dados, anexa os ficheiros referenciados pelo conjunto de dados a um diretório (ponto de montagem) e disponibiliza-os no alvo do cálculo. A montagem é suportada para computas baseadas em Linux, incluindo Azure Machine Learning Compute, máquinas virtuais e HDInsight. 

Quando **descarrega** um conjunto de dados, todos os ficheiros referenciados pelo conjunto de dados serão descarregados para o alvo do cálculo. O download é suportado para todos os tipos de computação. 

Se o seu script processar todos os ficheiros referenciados pelo conjunto de dados e o seu disco de computação puder encaixar no conjunto de dados completo, recomenda-se o download para evitar a sobrecarga de dados de streaming dos serviços de armazenamento. Se o tamanho dos dados exceder o tamanho do disco computacional, o download não é possível. Para este cenário, recomendamos a montagem, uma vez que apenas os ficheiros de dados utilizados pelo seu script são carregados no momento do processamento.

O seguinte código monta `dataset` para o diretório temporário em `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Obtenha conjuntos de dados em scripts de aprendizagem automática

Os conjuntos de dados registados são acessíveis localmente e remotamente em clusters computacional como o cálculo Azure Machine Learning. Para aceder ao conjunto de dados registado através de experiências, utilize o seguinte código para aceder ao seu espaço de trabalho e obter o conjunto de dados que foi utilizado na sua execução previamente submetida. Por predefinição, [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) o método na classe devolve a versão mais recente do conjunto de `Dataset` dados registado no espaço de trabalho.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Código fonte de acesso durante o treino

O armazenamento Azure Blob tem velocidades de produção mais altas do que uma partilha de ficheiros Azure e irá escalar para um grande número de empregos iniciados em paralelo. Por esta razão, recomendamos configurar as suas corridas para utilizar o armazenamento Blob para transferir ficheiros de código fonte.

O exemplo de código a seguir especifica na configuração de execução que blob datastore para usar para transferências de código fonte.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Exemplos de cadernos

+ Para obter exemplos e conceitos adicionais de conjunto de dados, consulte os [cadernos do conjunto de dados](https://aka.ms/dataset-tutorial).
+ Veja como [parametrizar conjuntos de dados nos seus oleodutos ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="troubleshooting"></a>Resolução de problemas

* **A inicialização do conjunto de dados falhou: Esperar que o ponto de montagem esteja pronto tem tempo esgotado**: 
  * Se não tiver nenhuma regra do [grupo de segurança](../virtual-network/network-security-groups-overview.md) de rede de saída e estiver a utilizar , `azureml-sdk>=1.12.0` atualização e as suas `azureml-dataset-runtime` dependências para ser a mais recente versão menor específica, ou se estiver a usá-la numa corrida, recrie o seu ambiente para que possa ter o mais recente patch com a correção. 
  * Se estiver a `azureml-sdk<1.12.0` utilizar, atualize para a versão mais recente.
  * Se tiver regras NSG de saída, certifique-se de que existe uma regra de saída que permite todo o tráfego para a etiqueta de serviço `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Armazenamento AzureFile sobrecarregado

Se receber um `Unable to upload project files to working directory in AzureFile because the storage is overloaded` erro, aplique após soluções alternativas.

Se estiver a utilizar a partilha de ficheiros para outras cargas de trabalho, como a transferência de dados, a recomendação é utilizar blobs para que a partilha de ficheiros seja gratuita para ser utilizada para a apresentação de execuções. Também pode dividir a carga de trabalho entre dois espaços de trabalho diferentes.

### <a name="passing-data-as-input"></a>Passar dados como entrada

*  **TypeError: FileNotFound: Não existe tal ficheiro ou diretório**: Este erro ocorre se o caminho do ficheiro que fornece não estiver onde o ficheiro está localizado. Tem de se certificar de que a forma como se refere ao ficheiro é consistente com o local onde montou o conjunto de dados no seu alvo de computação. Para garantir um estado determinístico, recomendamos a utilização do caminho abstrato ao montar um conjunto de dados para um alvo computacional. Por exemplo, no seguinte código montamos o conjunto de dados sob a raiz do sistema de ficheiros do alvo do computação, `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se não incluir o corte dianteiro principal, '/', terá de pré-fixar o diretório de trabalho, por exemplo, `/mnt/batch/.../tmp/dataset` no alvo do cálculo para indicar onde pretende que o conjunto de dados seja montado.


## <a name="next-steps"></a>Passos seguintes

* [Modelos de aprendizagem automática de máquinas](how-to-configure-auto-train.md#data-source-and-format) de comboio com Separadors.

* [Treinar modelos de classificação de imagem](https://aka.ms/filedataset-samplenotebook) com FileDatasets.

* [Treine com conjuntos de dados utilizando gasodutos.](./how-to-create-machine-learning-pipelines.md)
