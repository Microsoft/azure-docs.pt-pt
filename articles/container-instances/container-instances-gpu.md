---
title: Implementar instâncias de contentor do Azure com GPU ativada
description: Saiba como implementar instâncias de contentor do Azure para executar nos recursos GPU.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/29/2018
ms.author: danlep
ms.openlocfilehash: cc47ca07a843daf5cc35d23b838761166d39bdcc
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351376"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Implementar instâncias de contentor que utilizam recursos GPU

Para executar determinadas cargas de trabalho intensivas de computação no Azure Container Instances, implementar sua [grupos de contentores](container-instances-container-groups.md) com *recursos GPU*. As instâncias de contentores no grupo podem aceder a um ou mais NVIDIA das GPUs Tesla ao executar cargas de trabalho do contentor como CUDA e aprendizagem profunda de aplicativos.

Como é mostrado neste artigo, pode adicionar recursos GPU quando implementar um grupo de contentores com um [ficheiro YAML](container-instances-multi-container-yaml.md) ou [modelo do Resource Manager](container-instances-multi-container-group.md).

> [!IMPORTANT]
> Esta funcionalidade está atualmente em pré-visualização e algumas [limitações aplicam-se](#preview-limitations). As pré-visualizações ser-lhe-ão disponibilizadas na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de pré-visualização

Em pré-visualização, as seguintes limitações aplicam-se ao usar os recursos GPU em grupos de contentores. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Será adicionado suporte para regiões adicionais ao longo do tempo.

**OS tipos suportados**: Apenas Linux

**Limitações adicionais**: Não não possível utilizar recursos GPU quando implementar um grupo de contentores para um [rede virtual](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Sobre os recursos GPU

### <a name="count-and-sku"></a>Contagem e SKU

Para utilizar GPUs numa instância de contentor, especifique um *recurso GPU* com as seguintes informações:

* **Contagem de** -o número de GPUs: **1**, **2**, ou **4**.
* **SKU** -o SKU de GPU: **K80**, **P100**, ou **V100**. Cada SKU é mapeado para GPU NVIDIA Tesla nas famílias VM ativadas para GPU do Azure seguintes:

  | SKU | Família de VM |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Ao implementar recursos GPU, defina os recursos de CPU e memória adequado para a carga de trabalho, até os valores máximos mostrado na tabela anterior. Estes valores são atualmente maiores do que os recursos de CPU e memória disponíveis em grupos de contentores sem recursos GPU.  

### <a name="things-to-know"></a>Que precisa saber

* **Tempo de implantação** -criação de um grupo de contentor que contém os recursos GPU demora até **8 a 10 minutos**. Isso se deve mais tempo para aprovisionar e configurar uma VM de GPU no Azure. 

* **Preços** - semelhantes para grupos de contentores sem recursos GPU, o Azure cobra pelos recursos consumidos através do *duração* de um grupo de contentores com os recursos GPU. A duração é calculada desde o momento para solicitar a imagem de seu primeiro contentor até que o grupo de contentor termine. Não inclui o tempo para implementar o grupo de contentores.

  O preço é superior para grupos de contentores com os recursos GPU que para grupos de contentores sem. Ver [os detalhes dos preços](https://azure.microsoft.com/pricing/details/container-instances/).

* **Controladores CUDA** - Container instances com recursos GPU são pré-aprovisionados com controladores de NVIDIA CUDA e tempos de execução do contentor, pelo que pode utilizar imagens de contentor desenvolvido para cargas de trabalho CUDA.

  Suportamos CUDA 9.0 nesta fase. Por exemplo, pode utilizar imagens de base para o ficheiro de Docker a seguir:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Exemplo YAML

Uma forma de adicionar recursos GPU é para implementar um grupo de contentores com um [ficheiro YAML](container-instances-multi-container-yaml.md). Copie o YAML seguinte para um novo ficheiro designado *aci.yaml gpu implementar*, em seguida, guarde o ficheiro. Este YAML cria um grupo de contentores com o nome *gpucontainergroup* especificando uma instância de contentor com uma GPU K80. A instância é executada uma aplicação de adição de vetor do exemplo CUDA. As solicitações de recursos são suficientes para executar a carga de trabalho.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Implementar o grupo de contentores com o [criar contentor de az] [ az-container-create] comando, especificando o nome de ficheiro YAML para o `--file` parâmetro. Precisa fornecer o nome de um grupo de recursos e uma localização para o grupo de contentores, como *eastus* que oferece suporte a recursos GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

A conclusão da implementação demora vários minutos. Em seguida, o contentor é iniciado e executa uma operação de adição de vetor CUDA. Executar o [registos de contentor az] [ az-container-logs] comando para ver a saída de registo:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Saída:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Exemplo de modelo do Resource Manager

Outra forma de implementar um grupo de contentores com os recursos GPU é usando um [modelo do Resource Manager](container-instances-multi-container-group.md). Comece por criar um arquivo chamado `gpudeploy.json`, em seguida, copie o JSON seguinte para o mesmo. Este exemplo implementa uma instância de contentor com uma GPU V100 que executa uma [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) tarefa de preparação contra o [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/). As solicitações de recursos são suficientes para executar a carga de trabalho.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Implementar o modelo com o [criar a implementação do grupo az] [ az-group-deployment-create] comando. Precisa fornecer o nome do grupo de recursos que foi criado numa região, tal como *eastus* que oferece suporte a recursos GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

A conclusão da implementação demora vários minutos. Em seguida, o contentor é iniciado e executa a tarefa do TensorFlow. Executar o [registos de contentor az] [ az-container-logs] comando para ver a saída de registo:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Saída:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Limpar recursos

Como utilizar os recursos GPU pode ser Caro, certifique-se de que os contentores não são executados inesperadamente por longos períodos. Monitorizar os seus contentores no portal do Azure ou verificar o estado de um grupo de contentores com o [show de contentor az] [ az-container-show] comando. Por exemplo:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Quando tiver terminado a trabalhar com as instâncias de contentor que criou, eliminá-los com os seguintes comandos:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a implementação de um grupo de contentor com um [ficheiro YAML](container-instances-multi-container-yaml.md) ou [modelo do Resource Manager](container-instances-multi-container-group.md).
* Saiba mais sobre [tamanhos de VM com otimização de GPU](../virtual-machines/linux/sizes-gpu.md) no Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
