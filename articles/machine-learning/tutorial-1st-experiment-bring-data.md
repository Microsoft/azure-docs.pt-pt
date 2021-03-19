---
title: 'Tutorial: Use os seus próprios dados'
titleSuffix: Azure Machine Learning
description: A parte 4 da série Azure Machine Learning mostra como usar os seus próprios dados num treino remoto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: tracking-python
ms.openlocfilehash: ecabfde624ba6d3393bbf6d5480b83dbb5303c5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604564"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Tutorial: Use os seus próprios dados (parte 4 de 4)

Este tutorial mostra-lhe como carregar e usar os seus próprios dados para treinar modelos de aprendizagem automática em Azure Machine Learning.

Este tutorial é *parte 4 de uma série tutorial* de quatro partes na qual você aprende os fundamentos do Azure Machine Learning e completa tarefas de aprendizagem automática baseadas em empregos em Azure. Este tutorial baseia-se no trabalho que concluiu na [Parte 1: Configurar,](tutorial-1st-experiment-sdk-setup-local.md) [Parte 2: Executar "Hello World!"](tutorial-1st-experiment-hello-world.md)e [Parte 3: Treine um modelo](tutorial-1st-experiment-sdk-train.md).

Na [Parte 3: Treine um modelo,](tutorial-1st-experiment-sdk-train.md)os dados foram descarregados através do método incorporado `torchvision.datasets.CIFAR10` na API PyTorch. No entanto, em muitos casos, você vai querer usar os seus próprios dados em um treino remoto. Este artigo mostra o fluxo de trabalho que pode utilizar para trabalhar com os seus próprios dados em Azure Machine Learning.

Neste tutorial:

> [!div class="checklist"]
> * Configure um script de treino para usar dados num diretório local.
> * Teste o roteiro de treino localmente.
> * Faça upload de dados para a Azure.
> * Criar um script de controlo.
> * Compreenda os novos conceitos de Aprendizagem automática Azure (passando parâmetros, conjuntos de dados, datastores).
> * Submeta e execute o seu roteiro de treino.
> * Veja a saída do seu código na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos dados e de uma versão atualizada do ambiente pytorch criado no tutorial anterior.  Certifique-se de ter concluído estes passos:

1. [Criar o script de preparação](tutorial-1st-experiment-sdk-train.md#create-training-scripts)
1. [Criar um novo ambiente Python](tutorial-1st-experiment-sdk-train.md#environment)
1. [Teste local](tutorial-1st-experiment-sdk-train.md#test-local)
1. [Atualizar o ficheiro ambiente Conda](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)

## <a name="adjust-the-training-script"></a>Ajuste o roteiro de treino

Por esta altura já tem o seu script de treino (tutorial/src/train.py) a funcionar em Azure Machine Learning, e pode monitorizar o desempenho do modelo. Vamos parametrizar o roteiro de treino introduzindo argumentos. A utilização de argumentos permitirá comparar facilmente diferentes hiperparímetros diferentes.

O nosso script de formação está agora definido para descarregar o conjunto de dados DO CIFAR10 em cada corrida. O seguinte código Python foi ajustado para ler os dados de um diretório.

>[!NOTE] 
> O uso de `argparse` parametriza o script.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>Compreender as alterações de código

O código `train.py` in usou a `argparse` biblioteca para `data_path` configurar, e `learning_rate` `momentum` .

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Além disso, o `train.py` script foi adaptado para atualizar o otimizador para usar os parâmetros definidos pelo utilizador:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

> [!div class="nextstepaction"]
> [Ajustei o guião de treino](?success=adjust-training-script#test-locally) [que encontrei num problema.](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> Teste o guião localmente

O seu script aceita agora _o caminho dos dados_ como argumento. Para começar, teste-o localmente. Adicione à sua estrutura de diretório tutorial uma pasta chamada `data` . A sua estrutura de diretório deve parecer:

:::image type="content" source="media/tutorial-1st-experiment-bring-data/directory-structure.png" alt-text="A estrutura do diretório mostra sub-directórios .azureml, dados e sub-directórios de SRC":::

1. Saia do ambiente atual.

    ```bash
    conda deactivate

1. Now create and activate the new environment.  This will rebuild the pytorch-aml-env with the [updated environment file](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)


    ```bash
    conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
    ```

    ```bash
    conda activate pytorch-aml-env          # activate new conda environment
    ```

1. Finalmente, executar o roteiro de treino modificado localmente.

    ```bash
    python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
    ```

Evita ter de descarregar o conjunto de dados do CIFAR10 passando por um caminho local para os dados. Você também pode experimentar com diferentes valores para _taxa de aprendizagem_ e hiperparímetros _de impulso_ sem ter que os codificar no script de treino.

> [!div class="nextstepaction"]
> [Testei o guião localmente](?success=test-locally#upload) [e encontrei um problema.](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Faça o upload dos dados para a Azure

Para executar este script em Azure Machine Learning, você precisa disponibilizar seus dados de treino em Azure. O seu espaço de trabalho Azure Machine Learning vem equipado com uma datastore _padrão._ Esta é uma conta de Armazenamento Azure Blob onde pode armazenar os seus dados de treino.

>[!NOTE] 
> O Azure Machine Learning permite-lhe conectar outras lojas de dados baseadas na nuvem que armazenam os seus dados. Para mais detalhes, consulte a [documentação da DatasTores.](./concept-data.md)  

Crie um novo script de controlo Python chamado `05-upload-data.py` no `tutorial` diretório:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

O `target_path` valor especifica a trajetória na loja de dados onde os dados do CIFAR10 serão carregados.

>[!TIP] 
> Enquanto estiver a utilizar o Azure Machine Learning para fazer o upload dos dados, pode utilizar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para carregar ficheiros ad hoc. Se precisar de uma ferramenta ETL, pode utilizar [a Azure Data Factory](../data-factory/introduction.md) para ingerir os seus dados no Azure.

Na janela que tem o ambiente *de conda tutorial1* ativado, execute o ficheiro Python para carregar os dados. (O upload deve ser rápido, menos de 60 segundos.)

```bash
python 05-upload-data.py
```

Deve ver a seguinte saída padrão:

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [Eu carreguei os dados](?success=upload-data#control-script) [que encontrei para um problema](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> Criar um script de controlo

Como já fez anteriormente, crie um novo script de controlo Python chamado `06-run-pytorch-data.py` :

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Compreender as alterações de código

O script de controlo é semelhante ao da [parte 3 desta série,](tutorial-1st-experiment-sdk-train.md)com as seguintes novas linhas:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Um [conjunto de dados](/python/api/azureml-core/azureml.core.dataset.dataset) é utilizado para fazer referência aos dados enviados para o Azure Blob Storage. Os conjuntos de dados são uma camada de abstração em cima dos seus dados que são projetados para melhorar a fiabilidade e fiabilidade.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) é modificado para incluir uma lista de argumentos que serão transmitidos em `train.py` . O `dataset.as_named_input('input').as_mount()` argumento significa que o diretório especificado será _montado_ no alvo do cálculo.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Criei o guião de controlo](?success=control-script#submit-to-cloud) [que encontrei num problema.](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Submeta a corrida para Azure Machine Learning

Agora reenvia a execução para utilizar a nova configuração:

```bash
python 06-run-pytorch-data.py
```

Este código irá imprimir um URL para a experiência no estúdio Azure Machine Learning. Se fores para esse link, poderás ver o teu código a funcionar.

> [!div class="nextstepaction"]
> [Resubmeti-me a correr](?success=submit-to-cloud#inspect-log) [e encontrei um problema.](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Inspecione o ficheiro de registo

No estúdio, vá à experiência executada (selecionando a saída de URL anterior) seguida de **Outputs + logs**. Selecione o `70_driver_log.txt` ficheiro. Deverá ver o seguinte resultado:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Aviso:

- A Azure Machine Learning montou o Blob Storage para o cluster de cálculo automaticamente para si.
- O ``dataset.as_named_input('input').as_mount()`` usado no script de controlo resolve-se até ao ponto de montagem.

> [!div class="nextstepaction"]
> [Inspecionei o ficheiro de registo](?success=inspect-log#clean-up-resources) que [encontrei num problema.](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e selecionar **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, vimos como enviar dados para a Azure utilizando `Datastore` . A datastore serviu como armazenamento em nuvem para o seu espaço de trabalho, dando-lhe um lugar persistente e flexível para manter os seus dados.

Viu como modificar o seu script de treino para aceitar um caminho de dados através da linha de comando. Ao `Dataset` utilizar, conseguiu montar um diretório para o funcionao remoto. 

Agora que tem um modelo, aprenda:

* Como [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).
