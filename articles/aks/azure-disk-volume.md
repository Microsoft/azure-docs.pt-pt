---
title: Criar um volume estático para cápsulas no Serviço Azure Kubernetes (AKS)
description: Saiba como criar manualmente um volume com discos Azure para utilização com uma cápsula no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 617ad75eda766963a91fe3d41b1dbfefae62b41b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776218"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Criar e utilizar de forma manual um volume com discos do Azure no Azure Kubernetes Service (AKS)

As aplicações baseadas em contentores precisam frequentemente de aceder e persistir dados num volume de dados externo. Se uma única cápsula precisar de acesso ao armazenamento, pode utilizar discos Azure para apresentar um volume nativo para uso de aplicações. Este artigo mostra-lhe como criar manualmente um disco Azure e anexá-lo a uma cápsula em AKS.

> [!NOTE]
> Um disco Azure só pode ser montado numa única cápsula de cada vez. Se precisar de partilhar um volume persistente em várias cápsulas, utilize [ficheiros Azure][azure-files-volume].

Para obter mais informações sobre volumes kubernetes, consulte [as opções de Armazenamento para aplicações em AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="create-an-azure-disk"></a>Criar um disco Azure

Quando criar um disco Azure para utilização com AKS, pode criar o recurso de disco no grupo de recursos **do nó.** Esta abordagem permite ao cluster AKS aceder e gerir o recurso de disco. Se, em vez disso, criar o disco num grupo de recursos separados, deve conceder ao Serviço Azure Kubernetes (AKS) identidade gerida para o seu cluster o `Contributor` papel no grupo de recursos do disco.

Para este artigo, crie o disco no grupo de recursos do nó. Primeiro, obtenha o nome do grupo de recursos com o comando [az aks show][az-aks-show] e adicione o `--query nodeResourceGroup` parâmetro de consulta. O exemplo a seguir obtém o grupo de recursos de nó para o nome de cluster AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora crie um disco utilizando o [comando de criação de disco az.][az-disk-create] Especificar o nome do grupo de recursos de nó obtido no comando anterior e, em seguida, um nome para o recurso de disco, como *o myAKSDisk*. O exemplo a seguir cria um disco *de 20* GiB e produz o ID do disco uma vez criado. Se precisar de criar um disco para utilização com recipientes do Windows Server, adicione o `--os-type windows` parâmetro para formatar corretamente o disco.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Os discos Azure são faturados pela SKU por um tamanho específico. Estes SKUs variam de 32GiB para discos S4 ou P4 a 32TiB para discos S80 ou P80 (em pré-visualização). O desempenho de produção e IOPS de um disco gerido Premium depende tanto do SKU como do tamanho dos nós no cluster AKS. Ver [Preços e Desempenho dos Discos Geridos][managed-disk-pricing-performance].

O ID do recurso do disco é apresentado uma vez que o comando tenha sido concluído com sucesso, como mostrado na saída do exemplo seguinte. Este ID do disco é utilizado para montar o disco no passo seguinte.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Monte o disco como volume

Para montar o disco Azure na sua vagem, configuure o volume na especificação do recipiente. Crie um novo ficheiro com `azure-disk-pod.yaml` o seguinte conteúdo. Atualize `diskName` com o nome do disco criado no passo anterior, e com o `diskURI` ID do disco mostrado na saída do disco criar comando. Se desejar, atualize o `mountPath` , que é o caminho onde o disco Azure é montado na vagem. Para os recipientes do Windows Server, especifique um *mountPath* utilizando a convenção do caminho do Windows, como *'D:'*.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Use o `kubectl` comando para criar a cápsula.

```console
kubectl apply -f azure-disk-pod.yaml
```

Tem agora uma cápsula de corrida com um disco Azure montado em `/mnt/azure` . Pode utilizar `kubectl describe pod mypod` para verificar se o disco está montado com sucesso. A seguinte saída de exemplo condensada mostra o volume montado no recipiente:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Passos seguintes

Para as melhores práticas associadas, consulte [as melhores práticas de armazenamento e backups em AKS][operator-best-practices-storage].

Para obter mais informações sobre os clusters AKS interagem com discos Azure, consulte o [plugin Kubernetes para Discos Azure][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az_resource_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
