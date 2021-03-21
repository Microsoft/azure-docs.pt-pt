---
title: Migrar dos Avaliadores para ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Guia de migração para migração de Estimadores para ScriptRunConfig para configurar trabalhos de formação.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ae0623a11b940a4d142f6bfae02d4b20727a6f55
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518877"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migrando de Estimadores para ScriptRunConfig

Até agora, tem havido vários métodos para configurar um trabalho de formação em Azure Machine Learning através do SDK, incluindo Estimadores, ScriptRunConfig, e a Configuração de Execução de nível inferior.   Para resolver esta ambiguidade e inconsistência, estamos a simplificar o processo de configuração de emprego no Azure ML.  Deve agora utilizar o ScriptRunConfig como a opção recomendada para configurar trabalhos de formação. 

Os estimadores são precfetados com a libertação 1.19.0 do Python SDK. Você também deve evitar explicitamente instantaneamente um objeto RunConfiguration e, em vez disso, configurar o seu trabalho usando a classe ScriptRunConfig.

Este artigo abrange considerações comuns ao migrar de Estimadores para ScriptRunConfig.

> [!IMPORTANT]
> Para migrar para ScriptRunConfig de Estimadores, certifique-se de que está a usar >= 1.15.0 do Python SDK.

## <a name="scriptrunconfig-documentation-and-samples"></a>Documentação e amostras do ScriptRunConfig
A documentação e amostras de Machine Learning Azure foram atualizadas para utilizar [o ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) para configuração e submissão de emprego.

Para obter informações sobre a utilização do ScriptRunConfig, consulte a seguinte documentação:
* [Configurar e enviar execuções de preparação](how-to-set-up-training-targets.md)
* [Configurar as corridas de treinamento pyTorch](how-to-train-pytorch.md)
* [Configurar as corridas de treino do TensorFlow](how-to-train-tensorflow.md)
* [Configurar as corridas de treino scikit-learn](how-to-train-scikit-learn.md)

Além disso, consulte as seguintes amostras & tutoriais:
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Exemplos Azure/azureml](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>Definição do ambiente de formação
Embora os vários estimadores-quadro tenham ambientes pré-configurados que são apoiados por imagens de Docker, os Dockerfiles para estas imagens são privados.  Por conseguinte, não tem muita transparência no que estes ambientes contêm. Além disso, os estimadores tomam configurações relacionadas com o ambiente como parâmetros individuais (tais `pip_packages` como, `custom_docker_image` ) nos respetivos construtores.

Ao utilizar o ScriptRunConfig, todas as configurações relacionadas com o ambiente são encapsuladas no `Environment` objeto que é passado para o parâmetro do construtor `environment` ScriptRunConfig. Para configurar um trabalho de formação, forneça um ambiente que tenha todas as dependências necessárias para o seu roteiro de treino. Se não for fornecido nenhum ambiente, o Azure ML utilizará uma das imagens de base Azure ML, especificamente a definida por `azureml.core.environment.DEFAULT_CPU_IMAGE` , como o ambiente padrão. Há algumas maneiras de proporcionar um ambiente:

* [Use um ambiente curado](how-to-use-environments.md#use-a-curated-environment) - ambientes curados são ambientes predefinidos disponíveis no seu espaço de trabalho por padrão. Existe um ambiente com curadoria correspondente para cada uma das imagens estivas estivas pré-configuradas que apoiaram cada estimador-quadro.
* [Defina o seu próprio ambiente personalizado](how-to-use-environments.md)

Aqui está um exemplo de utilização do ambiente PyTorch 1.6 curado para a formação:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Se quiser especificar **variáveis ambientais** que serão definidas no processo em que o script de treino é executado, utilize o objeto Ambiente:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Para obter informações sobre a configuração e gestão dos ambientes Azure ML, consulte:
* [Como utilizar ambientes](how-to-use-environments.md)
* [Ambientes organizados](resource-curated-environments.md)
* [Trem com uma imagem personalizada do Docker](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Utilização de dados para formação
### <a name="datasets"></a>Conjuntos de dados
Se estiver a utilizar um conjunto de dados Azure ML para treino, passe o conjunto de dados como argumento para o seu script utilizando o `arguments` parâmetro. Ao fazê-lo, obterá o caminho dos dados (ponto de montagem ou caminho de descarregamento) no seu script de treino através de argumentos.

O exemplo a seguir configura um trabalho de formação onde o FileDataset, `mnist_ds` será montado no computação remoto.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (antiga)
Embora recomendemos a utilização de conjuntos de dados Azure ML sobre a antiga forma de DataReference, se ainda estiver a utilizar DataReferences por qualquer motivo, deve configurar o seu trabalho da seguinte forma:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Para obter mais informações sobre a utilização de dados para formação, consulte:
* [Treine com conjuntos de dados em Azure ML](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>Preparação distribuída
Se precisar de configurar um trabalho distribuído para a formação, faça-o especificando o `distributed_job_config` parâmetro no construtor ScriptRunConfig. Passe numa [Configuração de Mpi,](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) [PyTorchConfiguration,](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)ou [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) para trabalhos distribuídos dos respetivos tipos.

O exemplo a seguir configura um trabalho de formação PyTorch para utilizar a formação distribuída com MPI/Horovod:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Para obter mais informações, consulte:
* [Preparação distribuída com o PyTorch](how-to-train-pytorch.md#distributed-training)
* [Treino distribuído com TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Diversos
Se precisar de aceder ao objeto RunConfiguration subjacente por um ScriptRunConfig por qualquer motivo, pode fazê-lo da seguinte forma:
```
src.run_config
```

## <a name="next-steps"></a>Passos seguintes

* [Configurar e enviar execuções de preparação](how-to-set-up-training-targets.md)