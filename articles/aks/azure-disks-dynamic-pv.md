---
title: Criar dinamicamente um volume de disco para vários pods no Azure Kubernetes Service (AKS)
description: Saiba como criar dinamicamente um volume persistente com discos do Azure para utilização com vários pods em simultâneo no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 735be71faecb9882b13f6f536d43715139d0f4db
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342025"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dinamicamente criar e utilizar um volume persistente com discos do Azure no Azure Kubernetes Service (AKS)

Um volume persistente representa uma parte do armazenamento aprovisionado para utilização com pods do Kubernetes. Um volume persistente pode ser utilizado por um ou vários pods e pode ser dinamicamente ou estaticamente aprovisionado. Este artigo mostra-lhe como criar dinamicamente volumes persistentes com discos do Azure para utilização por um único pod num cluster do Azure Kubernetes Service (AKS).

> [!NOTE]
> Um disco do Azure só pode ser montado com *modo de acesso* tipo *ReadWriteOnce*, que torna disponíveis para um único pod no AKS. Se precisar de partilhar um volume persistente entre vários pods, utilize [ficheiros do Azure][azure-files-pvc].

Para obter mais informações sobre volumes do Kubernetes, consulte [opções de armazenamento para aplicações no AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="built-in-storage-classes"></a>Criado em classes de armazenamento

Uma classe de armazenamento é utilizada para definir como uma unidade de armazenamento é dinamicamente criada com um volume persistente. Para obter mais informações sobre classes de armazenamento do Kubernetes, consulte [Classes de armazenamento do Kubernetes][kubernetes-storage-classes].

Cada cluster do AKS inclui duas classes de armazenamento pré-criados, ambos configurado para trabalhar com os discos do Azure:

* O *predefinição* classe de armazenamento aprovisiona um disco standard do Azure.
    * Armazenamento Standard está protegido por HDDs e fornece armazenamento económico, mantendo o alto desempenho. Os discos Standard são ideais para uma carga de trabalho de desenvolvimento e teste económica.
* O *premium geridos* classe de armazenamento Aprovisiona uma premium disco do Azure.
    * Os discos Premium são apoiados por um disco de elevado desempenho baseado em SSD e de baixa latência. São perfeitos para as VMs com carga de trabalho de produção. Se os nós do AKS no seu cluster utilizam o armazenamento premium, selecione o *premium geridos* classe.

Utilize o [kubectl obter sc] [ kubectl-get] comando para ver as classes de armazenamento pré-criados. A exemplo a seguir mostra a pré-criar classes de armazenamento disponíveis dentro de um cluster do AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Volume persistente afirmações são especificadas em GiB mas Azure managed disks são faturados ao SKU para um tamanho específico. Estes intervalo de SKUs de 32GiB para S4 ou P4 discos a 32TiB para discos S80 ou P80 (em pré-visualização). O débito e o desempenho de IOPS de uma Premium geridos disco depende tanto o SKU e o tamanho das instâncias de nós do cluster do AKS. Para obter mais informações, consulte [preço e desempenho dos Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Criar uma afirmação de volume persistente

Uma afirmação de volume persistente (PVC) é utilizada para aprovisionar automaticamente o armazenamento com base numa classe de armazenamento. Neste caso, um PVC pode utilizar uma das classes de armazenamento pré-criados para criar um standard ou premium do Azure disco gerido.

Crie um ficheiro denominado `azure-premium.yaml`e a cópia no manifesto do seguinte. A afirmação solicita um disco chamado `azure-managed-disk` isto é *5GB* de tamanho com *ReadWriteOnce* acesso. O *premium geridos* classe de armazenamento está especificada como a classe de armazenamento.

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
> Para criar um disco que utiliza o armazenamento standard, utilize `storageClassName: default` em vez *premium geridos*.

Criar a afirmação de volume persistente com o [kubectl aplicam-se] [ kubectl-apply] de comandos e especificar seu *azure-premium.yaml* ficheiro:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Utilizar o volume persistente

Assim que tiver sido criada a afirmação de volume persistente e o disco foi aprovisionado com êxito, um pod pode ser criado com acesso ao disco. O manifesto seguinte cria um pod NGINX básico que utiliza com o nome de afirmações de volume persistente *disco do azure geridos* montar o disco do Azure no caminho `/mnt/azure`.

Crie um ficheiro denominado `azure-pvc-disk.yaml`e a cópia no manifesto do seguinte.

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

Criar o pod com o [aplicam-se de kubectl] [ kubectl-apply] de comando, conforme mostrado no exemplo a seguir:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Tem agora um pod em execução com o seu disco do Azure montado no `/mnt/azure` diretório. Esta configuração pode ser vista quando inspecionar o seu pod via `kubectl describe pod mypod`, conforme mostrado no seguinte exemplo condensado:

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

## <a name="back-up-a-persistent-volume"></a>Fazer uma cópia de segurança de um volume persistente

Para fazer backup dos dados no seu volume persistente, tire um instantâneo do disco gerido para o volume. Em seguida, pode utilizar este instantâneo para criar um disco restaurado e anexar a pods como um meio de restauro dos dados.

Em primeiro lugar, obtenha o nome de volume com o `kubectl get pvc` comando, tal como para PVC com o nome *disco do azure gerido*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Este nome de volume forma o nome de disco do Azure subjacente. Consulta para o ID de disco com [lista de disco de az] [ az-disk-list] e indique o seu nome de volume PVC, conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Utilize o ID do disco para criar um disco de instantâneo com [criar instantâneo de az][az-snapshot-create]. O exemplo seguinte cria um instantâneo com o nome *pvcSnapshot* no mesmo grupo de recursos que o cluster do AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Se criar instantâneos e restaurar discos em grupos de recursos que o cluster do AKS não tem acesso a, pode encontrar problemas de permissão.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Dependendo da quantidade de dados no disco, poderá demorar alguns minutos para criar o instantâneo.

## <a name="restore-and-use-a-snapshot"></a>Restaurar e utilizar um instantâneo

Para restaurar o disco e utilizá-lo com um pod de Kubernetes, utilize o instantâneo como uma origem ao criar um disco com [criar disco de az][az-disk-create]. Esta operação preserva o recurso original se, em seguida, precisa acessar o instantâneo de dados original. O exemplo seguinte cria um disco chamado *pvcRestored* partir do instantâneo com o nome *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Para utilizar o disco restaurado com um pod, especifica o ID do disco no manifesto. Obter o ID de disco com o [show de disco de az] [ az-disk-show] comando. O exemplo seguinte obtém o ID do disco para *pvcRestored* criado no passo anterior:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Criar um manifesto de pod com o nome `azure-restored.yaml` e especifique o disco URI que obteve no passo anterior. O exemplo seguinte cria um servidor de web NGINX básico, com o disco restaurado montado como um volume em */mnt/azure*:

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

Criar o pod com o [aplicam-se de kubectl] [ kubectl-apply] de comando, conforme mostrado no exemplo a seguir:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Pode usar `kubectl describe pod mypodrestored` para ver os detalhes de pod, como o seguinte exemplo condensado, que mostra as informações de volume:

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

## <a name="next-steps"></a>Passos Seguintes

Para as práticas recomendadas associadas, consulte [melhores práticas para o armazenamento e cópias de segurança no AKS][operator-best-practices-storage].

Saiba mais sobre os volumes de persistentes Kubernetes com os discos do Azure.

> [!div class="nextstepaction"]
> [Plug-in do Kubernetes para os discos do Azure][azure-disk-volume]

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
