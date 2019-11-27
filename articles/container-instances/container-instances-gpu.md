---
title: Implantar instância de contêiner habilitada para GPU
description: Saiba como implantar instâncias de contêiner do Azure para executar aplicativos de contêiner de uso intensivo de computação usando recursos de GPU.
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: ea3b0ccba2d84487356f4bbd404cec3af1d0979a
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484193"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Implantar instâncias de contêiner que usam recursos de GPU

Para executar determinadas cargas de trabalho de computação intensiva em instâncias de contêiner do Azure, implante seus [grupos de contêineres](container-instances-container-groups.md) com *recursos de GPU*. As instâncias de contêiner no grupo podem acessar uma ou mais GPUs NVIDIA Tesla durante a execução de cargas de trabalho de contêiner, como CUDA e aplicativos de aprendizado profundo.

Este artigo mostra como adicionar recursos de GPU ao implantar um grupo de contêineres usando um [arquivo YAML](container-instances-multi-container-yaml.md) ou um [modelo do Resource Manager](container-instances-multi-container-group.md). Você também pode especificar recursos de GPU ao implantar uma instância de contêiner usando o portal do Azure.

> [!IMPORTANT]
> Este recurso está atualmente em visualização e algumas [limitações se aplicam](#preview-limitations). As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de visualização

Na versão prévia, as seguintes limitações se aplicam ao usar recursos de GPU em grupos de contêineres. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

O suporte será adicionado para regiões adicionais ao longo do tempo.

**Tipos de so com suporte**: somente Linux

**Limitações adicionais**: os recursos de GPU não podem ser usados ao implantar um grupo de contêineres em uma [rede virtual](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Sobre os recursos de GPU

### <a name="count-and-sku"></a>Contagem e SKU

Para usar GPUs em uma instância de contêiner, especifique um *recurso de GPU* com as seguintes informações:

* **Contagem** – o número de GPUs: **1**, **2**ou **4**.
* **SKU** -a SKU de GPU: **K80**, **P100**ou **V100**. Cada SKU é mapeado para a GPU NVIDIA Tesla em uma das seguintes famílias de VMs habilitadas para a GPU do Azure:

  | SKU | Família de VMs |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Ao implantar recursos de GPU, defina os recursos de CPU e memória apropriados para a carga de trabalho, até os valores máximos mostrados na tabela anterior. Esses valores são atualmente maiores do que os recursos de CPU e memória disponíveis em grupos de contêineres sem recursos de GPU.  

### <a name="things-to-know"></a>Coisas a saber

* **Tempo de implantação** -a criação de um grupo de contêineres que contém recursos de GPU leva até **8-10 minutos**. Isso ocorre devido ao tempo adicional para provisionar e configurar uma VM GPU no Azure. 

* **Preço** – semelhante aos grupos de contêineres sem recursos de GPU, o Azure cobra pelos recursos consumidos durante a *duração* de um grupo de contêineres com recursos de GPU. A duração é calculada a partir do momento para extrair a imagem de seu primeiro contêiner até que o grupo de contêineres seja encerrado. Ele não inclui o tempo para implantar o grupo de contêineres.

  Veja os [detalhes dos preços](https://azure.microsoft.com/pricing/details/container-instances/).

* **Drivers CUDA** -as instâncias de contêiner com recursos de GPU são previamente provisionadas com drivers NVIDIA CUDA e tempos de execução de contêiner, para que você possa usar imagens de contêiner desenvolvidas para cargas de trabalho CUDA.

  Damos suporte ao CUDA 9,0 neste estágio. Por exemplo, você pode usar as seguintes imagens base para o arquivo do Docker:
  * [NVIDIA/CUDA: 9.0-base-Ubuntu 16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-GPU-PY3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Exemplo de YAML

Uma maneira de adicionar recursos de GPU é implantar um grupo de contêineres usando um [arquivo YAML](container-instances-multi-container-yaml.md). Copie o seguinte YAML em um novo arquivo chamado *GPU-Deploy-ACI. YAML*e salve o arquivo. Este YAML cria um grupo de contêineres chamado *gpucontainergroup* especificando uma instância de contêiner com uma GPU K80. A instância executa um aplicativo de adição de vetor CUDA de exemplo. As solicitações de recursos são suficientes para executar a carga de trabalho.

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

Implante o grupo de contêineres com o comando [AZ container Create][az-container-create] , especificando o nome do arquivo YAML para o parâmetro `--file`. Você precisa fornecer o nome de um grupo de recursos e um local para o grupo de contêineres, como *lesteus* , que oferece suporte a recursos de GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

A conclusão da implementação demora vários minutos. Em seguida, o contêiner inicia e executa uma operação de adição de vetor CUDA. Execute o comando [AZ container logs][az-container-logs] para exibir a saída do log:

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

Outra maneira de implantar um grupo de contêineres com recursos de GPU é usando um [modelo do Resource Manager](container-instances-multi-container-group.md). Comece criando um arquivo chamado `gpudeploy.json`e, em seguida, copie o JSON a seguir nele. Este exemplo implanta uma instância de contêiner com uma GPU V100 que executa um trabalho de treinamento [TensorFlow](https://www.tensorflow.org/) no conjunto de MNIST. As solicitações de recursos são suficientes para executar a carga de trabalho.

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

Implante o modelo com o comando [AZ Group Deployment Create][az-group-deployment-create] . Você precisa fornecer o nome de um grupo de recursos que foi criado em uma região, como *lesteus* , que oferece suporte a recursos de GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

A conclusão da implementação demora vários minutos. Em seguida, o contêiner inicia e executa o trabalho TensorFlow. Execute o comando [AZ container logs][az-container-logs] para exibir a saída do log:

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

Como o uso de recursos de GPU pode ser caro, certifique-se de que seus contêineres não sejam executados inesperadamente por longos períodos. Monitore seus contêineres no portal do Azure ou verifique o status de um grupo de contêineres com o comando [AZ container show][az-container-show] . Por exemplo:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Quando você terminar de trabalhar com as instâncias de contêiner criadas, exclua-as com os seguintes comandos:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como implantar um grupo de contêineres usando um [arquivo YAML](container-instances-multi-container-yaml.md) ou um [modelo do Resource Manager](container-instances-multi-container-group.md).
* Saiba mais sobre [tamanhos de VM otimizados para GPU](../virtual-machines/linux/sizes-gpu.md) no Azure.


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
