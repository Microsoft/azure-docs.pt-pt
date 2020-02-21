---
title: Implementar instância de contentores ativados por GPU
description: Aprenda a implementar instâncias de contentores Azure para executar aplicações de contentores intensivas em cálculo utilizando recursos GPU.
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3b202d1f35194d59090c3cc310226d6cfc4dfea
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482960"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Implementar instâncias de contentores que utilizam recursos GPU

Para executar certas cargas de trabalho intensivas em cálculos em Instâncias de Contentores Azure, coloque os seus [grupos de contentores](container-instances-container-groups.md) com *recursos GPU*. As instâncias de contentores do grupo podem aceder a uma ou mais GPUs Da NVIDIA Tesla enquanto executam cargas de trabalho de contentores, como CUDA e aplicações de aprendizagem profunda.

Este artigo mostra como adicionar recursos GPU quando implanta um grupo de contentores utilizando um [modelo de ficheiro YAML](container-instances-multi-container-yaml.md) ou [Desprovido](container-instances-multi-container-group.md)de recursos . Também pode especificar os recursos da GPU quando implementa uma instância de contentores utilizando o portal Azure.

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização, e [aplicam-se algumas limitações.](#preview-limitations) As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de pré-visualização

Na pré-visualização, aplicam-se as seguintes limitações ao utilizar recursos de GPU em grupos de contentores. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Serão adicionados apoios a regiões adicionais ao longo do tempo.

**Tipos de SO suportados**: Linux apenas

**Limitações adicionais**: Os recursos da GPU não podem ser utilizados ao implantar um grupo de contentores numa [rede virtual](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Sobre os recursos da GPU

### <a name="count-and-sku"></a>Conde e SKU

Para utilizar as GPUs numa instância de contentores, especifique um *recurso gpu* com as seguintes informações:

* **Contagem** - Número de GPUs: **1,** **2**, ou **4**.
* **SKU** - O GPU SKU: **K80,** **P100,** ou **V100**. Cada SKU mapeia para o GPU NVIDIA Tesla numa das seguintes famílias De VM ativadas por GPU:

  | SKU | Família VM |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Ao utilizar os recursos da GPU, detete toda a CPU e dos recursos de memória adequados à carga de trabalho, até aos valores máximos apresentados na tabela anterior. Estes valores são atualmente maiores do que o CPU e os recursos de memória disponíveis em grupos de contentores sem recursos de GPU.  

### <a name="things-to-know"></a>Coisas a saber

* Tempo de **implantação** - Criação de um grupo de contentores contendo recursos GPU demora até **8-10 minutos**. Isto deve-se ao tempo adicional para fornecer e configurar um GPU VM em Azure. 

* **Preços** - Semelhantes a grupos de contentores sem recursos gpu, o Azure fatura os recursos consumidos ao longo da *duração* de um grupo de contentores com recursos gpu. A duração é calculada a partir do momento para puxar a imagem do seu primeiro recipiente até que o grupo de contentores termine. Não inclui o tempo de implantação do grupo de contentores.

  Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/container-instances/)

* **Condutores cuda** - Os casos de contentores com recursos GPU são pré-provisionados com condutores da NVIDIA CUDA e tempos de execução de contentores, para que possa utilizar imagens de contentores desenvolvidas para cargas de trabalho cuda.

  Apoiamos a CUDA 9.0 nesta fase. Por exemplo, pode utilizar as seguintes imagens base para o seu ficheiro Docker:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Exemplo yAML

Uma forma de adicionar recursos gpu é implantar um grupo de contentores utilizando um [ficheiro YAML](container-instances-multi-container-yaml.md). Copie o seguinte YAML num novo ficheiro chamado *gpu-deploy-aci.yaml*e, em seguida, guarde o ficheiro. Este YAML cria um grupo de contentores chamado *gpucontainergroup* especificando uma instância de contentorcom uma GPU K80. A instância executa uma aplicação de adição de vetor CUDA amostra. Os pedidos de recursos são suficientes para executar a carga de trabalho.

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

Desloque o grupo de contentores com o [recipiente az criar][az-container-create] comando, especificando o nome de ficheiro YAML para o parâmetro `--file`. Você precisa fornecer o nome de um grupo de recursos e uma localização para o grupo de contentores, como *eastus* que suporta recursos de GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

A conclusão da implementação demora vários minutos. Em seguida, o recipiente começa e executa uma operação de adição de vetor CUDA. Executar o comando de registos de [recipientes az][az-container-logs] para visualizar a saída de registo:

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

## <a name="resource-manager-template-example"></a>Exemplo de modelo de gestor de recursos

Outra forma de implantar um grupo de contentores com recursos GPU é usando um modelo de Gestor de [Recursos](container-instances-multi-container-group.md). Comece por criar um ficheiro chamado `gpudeploy.json`, em seguida, copie o seguinte JSON nele. Este exemplo implementa uma instância de contentores com uma GPU V100 que executa um trabalho de formação [TensorFlow](https://www.tensorflow.org/) contra o conjunto de dados MNIST. Os pedidos de recursos são suficientes para executar a carga de trabalho.

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

Desloque o modelo com a implementação do [grupo AZ criar][az-group-deployment-create] comando. É preciso fornecer o nome de um grupo de recursos que foi criado numa região como o *Eastus* que apoia os recursos da GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

A conclusão da implementação demora vários minutos. Em seguida, o recipiente começa e executa o trabalho TensorFlow. Executar o comando de registos de [recipientes az][az-container-logs] para visualizar a saída de registo:

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

Uma vez que a utilização de recursos GPU pode ser dispendiosa, certifique-se de que os seus contentores não funcionam inesperadamente por longos períodos. Monitorize os seus contentores no portal Azure ou verifique o estado de um grupo de contentores com o comando do [contentor az.][az-container-show] Por exemplo:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Quando terminar de trabalhar com as instâncias do contentor que criou, elimine-as com os seguintes comandos:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a implementação de um grupo de contentores utilizando um [modelo de ficheiro YAML](container-instances-multi-container-yaml.md) ou [Desprovido](container-instances-multi-container-group.md)de recursos .
* Saiba mais sobre [os tamanhos de VM otimizados](../virtual-machines/linux/sizes-gpu.md) da GPU em Azure.


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
