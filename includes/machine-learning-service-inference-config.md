---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159421"
---
As entradas `inferenceconfig.json` no mapa do documento para os parâmetros da classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) A tabela seguinte descreve o mapeamento entre entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Caminho para um ficheiro local que contém o código para correr para a imagem. |
| `sourceDirectory` | `source_directory` | Opcional. Caminho para pastas que contenham todos os ficheiros para criar a imagem, o que facilita o acesso a quaisquer ficheiros dentro desta pasta ou subpasta. Pode fazer o upload de uma pasta inteira da sua máquina local como dependências para o Serviço Web. Nota: os seus caminhos entry_script, conda_file e extra_docker_file_steps são caminhos relativos para o caminho source_directory. |
| `environment` | `environment` | Opcional.  Ambiente de [aprendizagem](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)automática azure.|

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
