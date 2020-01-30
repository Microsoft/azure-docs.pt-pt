---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845141"
---
As entradas no mapa de documentos `inferenceconfig.json` para os parâmetros da classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) A tabela seguinte descreve o mapeamento entre entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Caminho para um ficheiro local que contém o código para correr para a imagem. |
| `runtime` | `runtime` | Opcional. Qual tempo de execução usar para a imagem. Os tempos de execução suportados são `spark-py` e `python`. Se `environment` for definida, esta entrada é ignorada. |
| `condaFile` | `conda_file` | Opcional. Caminho para um arquivo local que contém uma definição de ambiente Conda para usar para a imagem.  Se `environment` for definida, esta entrada é ignorada. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Opcional. Caminho para um ficheiro local que contém passos adicionais do Docker para executar ao configurar a imagem.  Se `environment` for definida, esta entrada é ignorada.|
| `sourceDirectory` | `source_directory` | Opcional. Caminho para pastas que contenham todos os ficheiros para criar a imagem, o que facilita o acesso a quaisquer ficheiros dentro desta pasta ou subpasta. Pode fazer o upload de uma pasta inteira da sua máquina local como dependências para o Serviço Web. Nota: os seus caminhos entry_script, conda_file e extra_docker_file_steps são caminhos relativos para o caminho source_directory. |
| `enableGpu` | `enable_gpu` | Opcional. Se o suporte à GPU deve ser habilitado na imagem. A imagem gpu deve ser utilizada num serviço Azure, como as Instâncias de Contentores Azure. Por exemplo, A Computação de Machine Learning Azure, máquinas virtuais Azure e serviço Azure Kubernetes. O padrão é falso. Se `environment` for definida, esta entrada é ignorada.|
| `baseImage` | `base_image` | Opcional. Imagem personalizada para ser usada como uma imagem base. Se não for fornecida nenhuma imagem base, a imagem será baseada no parâmetro de tempo de execução fornecido. Se `environment` for definida, esta entrada é ignorada. |
| `baseImageRegistry` | `base_image_registry` | Opcional. Registro de imagem que contém a imagem base. Se `environment` for definida, esta entrada é ignorada.|
| `cudaVersion` | `cuda_version` | Opcional. Versão do CUDA a ser instalada para imagens que precisam de suporte à GPU. A imagem da GPU deve ser utilizada num serviço Azure. Por exemplo, Instâncias de Contentores Azure, Computação de Machine Learning Azure, Máquinas Virtuais Azure e Serviço Azure Kubernetes. As versões com suporte são 9,0, 9,1 e 10,0. Se `enable_gpu` estiver definido, o predefinido é de 9.1. Se `environment` for definida, esta entrada é ignorada. |
| `description` | `description` | Uma descrição para a imagem. Se `environment` for definida, esta entrada é ignorada.  |
| `environment` | `environment` | Opcional.  Ambiente de [aprendizagem](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)automática azure.|

O jSON seguinte é uma configuração de inferência exemplo para utilização com o CLI:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

Pode incluir especificações completas de um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de aprendizagem automática Azure no ficheiro de configuração de inferência. Se este ambiente não existir no seu espaço de trabalho, o Azure Machine Learning irá criá-lo. Caso contrário, o Azure Machine Learning atualizará o ambiente se necessário. O seguinte JSON é um exemplo:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Também pode utilizar um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de aprendizagem automática Azure existente em parâmetros CLI separados e remover a tecla "ambiente" do ficheiro de configuração de inferência. Use -e para o nome do ambiente, e --ev para a versão ambiental. Se não especificar --ev, a versão mais recente será usada. Aqui está um exemplo de um ficheiro de configuração de inferência:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

O comando seguinte demonstra como implementar um modelo utilizando o ficheiro de configuração de inferência anterior (denominado myInferenceConfig.json). 

Também usa a versão mais recente de um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Aprendizagem automática Azure existente (chamado AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
