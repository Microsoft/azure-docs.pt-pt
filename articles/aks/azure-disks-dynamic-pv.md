---
title: Criar disquetes Azure disqueia disjuntores
titleSuffix: Azure Kubernetes Service
description: Aprenda a criar dinamicamente um volume persistente com discos Azure no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 9ac41b1738d1691f6547f508d1a38dec89b0bb79
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208147"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Criar e utilizar de forma dinâmica um volume persistente com discos Azure no Serviço Azure Kubernetes (AKS)

Um volume persistente representa um pedaço de armazenamento que foi provisionado para uso com cápsulas Kubernetes. Um volume persistente pode ser usado por uma ou muitas cápsulas, e pode ser provisodo dinamicamente ou estáticamente. Este artigo mostra-lhe como criar volumes persistentes com discos Azure para utilização por uma única cápsula num cluster do Serviço Azure Kubernetes (AKS).

> [!NOTE]
> Um disco Azure só pode ser montado com o tipo de *modo de acesso* *ReadWriteOnce,* que o disponibiliza apenas para uma única cápsula em AKS. Se precisar de partilhar um volume persistente em várias cápsulas, utilize [ficheiros Azure][azure-files-pvc].

Para obter mais informações sobre os volumes kubernetes, consulte as opções de [armazenamento para aplicações em AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="built-in-storage-classes"></a>Aulas de armazenamento embutidos

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre as aulas de armazenamento da Kubernetes, consulte as Aulas de [Armazenamento kubernetes.][kubernetes-storage-classes]

Cada cluster AKS inclui duas classes de armazenamento pré-criadas, ambas configuradas para trabalhar com discos Azure:

* A classe de armazenamento *padrão* prevê um disco Azure padrão.
    * O armazenamento padrão é apoiado por HDDs e fornece armazenamento rentável enquanto ainda é executante. Os discos standard são ideais para um dev e trabalho de trabalho de teste rentáveis.
* A classe de armazenamento *premium gerida* prevê um disco Azure premium.
    * Os discos Premium são apoiados por um disco de elevado desempenho baseado em SSD e de baixa latência. São perfeitos para as VMs com carga de trabalho de produção. Se os nós AKS do seu cluster utilizarem armazenamento premium, selecione a classe *premium gerida.*
    
Estas classes de armazenamento padrão não permitem atualizar o tamanho do volume uma vez criado. Para ativar esta capacidade, adicione a *allowVolumeExpansion: linha verdadeira* a uma das classes de armazenamento padrão, ou crie a sua própria classe de armazenamento personalizado. Pode editar uma classe de `kubectl edit sc` armazenamento existente utilizando o comando. Para obter mais informações sobre as classes de armazenamento e criar as suas próprias, consulte opções de [armazenamento para aplicações no AKS][storage-class-concepts].

Use o [kubectl obter][kubectl-get] comando sc para ver as classes de armazenamento pré-criadas. O exemplo que se segue mostra as classes de armazenamento pré-criar disponíveis dentro de um cluster AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> As reclamações de volume persistentes são especificadas no GiB, mas os discos geridos pelo Azure são faturados pela SKU para um tamanho específico. Estes SKUs variam de 32GiB para discos S4 ou P4 até 32TiB para discos S80 ou P80 (em pré-visualização). O desempenho de entrada e IOPS de um disco gerido premium depende tanto do SKU como do tamanho da instância dos nós no cluster AKS. Para mais informações, consulte [Preços e Desempenho de Discos Geridos][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Criar uma reivindicação persistente de volume

Uma reivindicação de volume persistente (PVC) é utilizada para fornecer automaticamente armazenamento com base numa classe de armazenamento. Neste caso, um PVC pode usar uma das classes de armazenamento pré-criadas para criar um disco gerido por Azure padrão ou premium.

Crie um `azure-premium.yaml`ficheiro com o nome e copie no seguinte manifesto. A reclamação solicita `azure-managed-disk` um disco nomeado com *5GB* de tamanho com acesso *ReadWriteOnce.* A classe de armazenamento *premium gerida* é especificada como a classe de armazenamento.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Para criar um disco que `storageClassName: default` utilize o armazenamento padrão, utilize em vez *de ser gerido.*

Crie a reivindicação de volume persistente com o [kubectl aplique][kubectl-apply] o comando e especifique o seu ficheiro *azure-premium.yaml:*

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Use o volume persistente

Uma vez criada a reivindicação persistente do volume e o disco aprovisionado com sucesso, uma cápsula pode ser criada com acesso ao disco. O manifesto seguinte cria uma cápsula básica NGINX que utiliza a reivindicação de volume persistente `/mnt/azure`chamada disco gerido por *azure* para montar o disco Azure no caminho . Para os recipientes do Windows Server, especifique um *mountPath* utilizando a convenção do caminho do Windows, como *'D:'*.

Crie um `azure-pvc-disk.yaml`ficheiro com o nome e copie no seguinte manifesto.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Crie a cápsula com o [kubectl aplique][kubectl-apply] o comando, como mostra o seguinte exemplo:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Agora tem uma cápsula de corrida com o `/mnt/azure` disco Azure montado no diretório. Esta configuração pode ser vista `kubectl describe pod mypod`ao inspecionar a sua cápsula via , como mostra o seguinte exemplo condensado:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Apoiar um volume persistente

Para fazer o backup dos dados no seu volume persistente, tire uma foto do disco gerido para o volume. Em seguida, pode utilizar este instantâneo para criar um disco restaurado e ligar-se às cápsulas como forma de restaurar os dados.

Em primeiro lugar, obtenha `kubectl get pvc` o nome de volume com o comando, como para o PVC denominado *disco gerido por azure:*

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Este nome de volume forma o nome do disco Azure subjacente. Consulta para o ID do disco com lista de [discos az][az-disk-list] e fornecer o seu nome de volume de PVC, como mostrado no exemplo seguinte:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Utilize o ID do disco para criar um disco instantâneo com [a z snapshot criar][az-snapshot-create]. O exemplo seguinte cria um instantâneo chamado *pvcSnapshot* no mesmo grupo de recursos que o cluster AKS *(MC_myResourceGroup_myAKSCluster_eastus).* Poderá encontrar problemas de permissão se criar instantâneos e restaurar discos em grupos de recursos aos quais o cluster AKS não tem acesso.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Dependendo da quantidade de dados no seu disco, pode levar alguns minutos para criar o instantâneo.

## <a name="restore-and-use-a-snapshot"></a>Restaurar e usar um instantâneo

Para restaurar o disco e usá-lo com uma cápsula Kubernetes, use o instantâneo como fonte quando criar um disco com [az disk criar][az-disk-create]. Esta operação preserva o recurso original se precisar de aceder ao instantâneo original. O exemplo seguinte cria um disco chamado *pvcRestaurado a* partir do instantâneo chamado *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Para utilizar o disco restaurado com uma cápsula, especifique a identificação do disco no manifesto. Obtenha a identificação do disco com o comando do [programa az disk.][az-disk-show] O exemplo seguinte obtém o ID do disco para *pvcRestored* criado no passo anterior:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Crie um `azure-restored.yaml` manifesto de pod nomeado e especifique o disco URI obtido no passo anterior. O exemplo seguinte cria um servidor web NGINX básico, com o disco restaurado montado como um volume em */mnt/azure:*

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Crie a cápsula com o [kubectl aplique][kubectl-apply] o comando, como mostra o seguinte exemplo:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Pode utilizar `kubectl describe pod mypodrestored` para visualizar detalhes da cápsula, como o seguinte exemplo condensado que mostra as informações de volume:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Passos seguintes

Para as melhores práticas associadas, consulte [as melhores práticas de armazenamento e backups em AKS][operator-best-practices-storage].

Saiba mais sobre os volumes persistentes da Kubernetes utilizando discos Azure.

> [!div class="nextstepaction"]
> [Plugin kubernetes para discos Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
