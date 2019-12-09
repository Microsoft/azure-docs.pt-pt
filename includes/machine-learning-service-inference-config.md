---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927013"
---
As entradas no documento `inferenceconfig.json` são mapeadas para os parâmetros da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . A tabela a seguir descreve o mapeamento entre entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Caminho para um arquivo local que contém o código a ser executado para a imagem. |
| `runtime` | `runtime` | Opcional. Qual tempo de execução usar para a imagem. Os tempos de execução com suporte atuais são `spark-py` e `python`. Se `environment` for definido, isso será ignorado. |
| `condaFile` | `conda_file` | Opcional. Caminho para um arquivo local que contém uma definição de ambiente Conda a ser usada para a imagem.  Se `environment` for definido, isso será ignorado. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Opcional. Caminho para um arquivo local que contém etapas adicionais do Docker para executar ao configurar a imagem.  Se `environment` for definido, isso será ignorado.|
| `sourceDirectory` | `source_directory` | Opcional. Caminho para pastas que contêm todos os arquivos para criar a imagem. |
| `enableGpu` | `enable_gpu` | Opcional. Se o suporte à GPU deve ser habilitado na imagem. A imagem GPU deve ser usada em um serviço do Azure, como instâncias de contêiner do Azure, Azure Machine Learning computação, máquinas virtuais do Azure e serviço kubernetes do Azure. A predefinição é False. Se `environment` for definido, isso será ignorado.|
| `baseImage` | `base_image` | Opcional. Imagem personalizada a ser usada como uma imagem de base. Se nenhuma imagem base for fornecida, a imagem será baseada no parâmetro de tempo de execução fornecido. Se `environment` for definido, isso será ignorado. |
| `baseImageRegistry` | `base_image_registry` | Opcional. Registro de imagem que contém a imagem base. Se `environment` for definido, isso será ignorado.|
| `cudaVersion` | `cuda_version` | Opcional. Versão do CUDA a ser instalada para imagens que precisam de suporte à GPU. A imagem GPU deve ser usada em um serviço do Azure, como instâncias de contêiner do Azure, Azure Machine Learning computação, máquinas virtuais do Azure e serviço kubernetes do Azure. As versões com suporte são 9,0, 9,1 e 10,0. Se `enable_gpu` for definido, o padrão será 9,1. Se `environment` for definido, isso será ignorado. |
| `description` | `description` | Uma descrição para a imagem. Se `environment` for definido, isso será ignorado.  |
| `environment` | `environment` | Opcional.  Azure Machine Learning [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

O JSON a seguir é uma configuração de inferência de exemplo para uso com a CLI:

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

O JSON a seguir é um exemplo de configuração de inferência que usa um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Azure Machine Learning existente com uma versão específica para uso com a CLI:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

O JSON a seguir é uma configuração de inferência de exemplo que usa um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Azure Machine Learning existente com a versão mais recente para uso com a CLI:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
