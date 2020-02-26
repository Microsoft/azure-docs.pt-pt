---
title: Criar um volume estático para cápsulas no Serviço Azure Kubernetes (AKS)
description: Aprenda a criar manualmente um volume com discos Azure para utilização com uma cápsula no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: b84f62dd02aa29a4c1aa64e3235c0a1e7cc66522
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596747"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Crie e use manualmente um volume com discos Azure no Serviço Azure Kubernetes (AKS)

As aplicações baseadas em contentores precisam frequentemente de aceder e persistir dados num volume externo de dados. Se uma única cápsula necessitar de acesso ao armazenamento, pode utilizar discos Azure para apresentar um volume nativo para utilização da aplicação. Este artigo mostra-lhe como criar manualmente um disco Azure e fixá-lo a uma cápsula em AKS.

> [!NOTE]
> Um disco Azure só pode ser montado numa única cápsula de cada vez. Se precisar de partilhar um volume persistente em várias cápsulas, utilize [ficheiros Azure][azure-files-volume].

Para obter mais informações sobre os volumes kubernetes, consulte as opções de [armazenamento para aplicações em AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="create-an-azure-disk"></a>Criar um disco Azure

Quando cria um disco Azure para utilização com AKS, pode criar o recurso do disco no grupo de recursos do **nó.** Esta abordagem permite ao cluster AKS aceder e gerir o recurso do disco. Se, em vez disso, criar o disco num grupo de recursos separado, deve conceder ao serviço do Serviço Azure Kubernetes (AKS) o papel `Contributor` ao grupo de recursos do disco.

Para este artigo, crie o disco no grupo de recursos do nó. Primeiro, obtenha o nome do grupo de recursos com o comando [az aks mostrar][az-aks-show] e adicionar o parâmetro de consulta `--query nodeResourceGroup`. O exemplo seguinte obtém o grupo de recursos do nó para o nome de cluster AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora crie um disco utilizando o [disco az criar][az-disk-create] comando. Especifique o nome do grupo de recursos do nó obtido no comando anterior e, em seguida, um nome para o recurso do disco, como *myAKSDisk*. O exemplo seguinte cria um disco de *20*GiB, e produz a identificação do disco uma vez criada. Se precisar de criar um disco para utilização com os recipientes do Windows Server (atualmente em pré-visualização no AKS), adicione o parâmetro `--os-type windows` para formatar corretamente o disco.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Os discos azure são faturados pela SKU para um tamanho específico. Estes SKUs variam de 32GiB para discos S4 ou P4 até 32TiB para discos S80 ou P80 (em pré-visualização). O desempenho de entrada e IOPS de um disco gerido premium depende tanto do SKU como do tamanho da instância dos nós no cluster AKS. Ver [Preços e Desempenho de Discos Geridos][managed-disk-pricing-performance].

O ID do recurso do disco é apresentado uma vez que o comando tenha sido concluído com sucesso, como mostra a seguinte saída de exemplo. Este ID do disco é usado para montar o disco no próximo passo.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Monte disco como volume

Para montar o disco Azure na sua cápsula, configure o volume na especificação do recipiente. Crie um novo ficheiro chamado `azure-disk-pod.yaml` com o seguinte conteúdo. Atualização `diskName` com o nome do disco criado no passo anterior, e `diskURI` com o ID do disco mostrado na saída do disco criar comando. Se desejar, atualize o `mountPath`, que é o caminho onde o disco Azure é montado na vagem. Para os recipientes do Windows Server (atualmente em pré-visualização no AKS), especifique um *mountPath* utilizando a convenção de caminhos windows, como *'D:'* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
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

Utilize o comando `kubectl` para criar a cápsula.

```console
kubectl apply -f azure-disk-pod.yaml
```

Agora tem uma cápsula de corrida com um disco Azure montado em `/mnt/azure`. Pode utilizar `kubectl describe pod mypod` para verificar se o disco está montado com sucesso. A saída de exemplo condensado seguinte mostra o volume montado no recipiente:

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

Para obter mais informações sobre os clusters AKS interaja com discos Azure, consulte o [plugin Kubernetes para Discos Azure][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
