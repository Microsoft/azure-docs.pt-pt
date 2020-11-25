---
title: Criar dinamicamente o volume dos discos Azure
titleSuffix: Azure Kubernetes Service
description: Aprenda a criar dinamicamente um volume persistente com discos Azure no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ad51bfdf8c494e763921de880926b839cdb7be62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021644"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Criar e utilizar dinamicamente um volume persistente com discos Azure no Serviço Azure Kubernetes (AKS)

Um volume persistente representa um pedaço de armazenamento que foi previsto para ser usado com cápsulas Kubernetes. Um volume persistente pode ser usado por uma ou muitas cápsulas, e pode ser dinamicamente ou estáticamente a provisionado. Este artigo mostra-lhe como criar dinamicamente volumes persistentes com discos Azure para uso por uma única cápsula num cluster Azure Kubernetes Service (AKS).

> [!NOTE]
> Um disco Azure só pode ser montado com *o tipo de modo de acesso* *ReadWriteOnce*, que o coloca à disposição de um nó em AKS. Se precisar de partilhar um volume persistente em vários nós, utilize [ficheiros Azure][azure-files-pvc].

Para obter mais informações sobre volumes kubernetes, consulte [as opções de Armazenamento para aplicações em AKS][concepts-storage].

## <a name="before-you-begin"></a>Before you begin

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="built-in-storage-classes"></a>Aulas de armazenamento embutindo

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre as aulas de armazenamento de Kubernetes, consulte [as Classes de Armazenamento Kubernetes.][kubernetes-storage-classes]

Cada cluster AKS inclui quatro aulas de armazenamento pré-criadas, duas delas configuradas para trabalhar com discos Azure:

* A classe de armazenamento *predefinida* prevê um disco Azure SSD padrão.
    * O armazenamento padrão é apoiado por SSDs standard e fornece armazenamento rentável, ao mesmo tempo que proporciona um desempenho fiável. 
* A classe de armazenamento *premium gerida* prevê um disco Azure premium.
    * Os discos Premium são apoiados por um disco de elevado desempenho baseado em SSD e de baixa latência. São perfeitos para as VMs com carga de trabalho de produção. Se os nós AKS no seu cluster usarem armazenamento premium, selecione a classe *premium gerida.*
    
Se utilizar uma das classes de armazenamento predefinidos, não poderá atualizar o tamanho do volume após a criação da classe de armazenamento. Para poder atualizar o tamanho do volume após a criação de uma classe de armazenamento, adicione a linha `allowVolumeExpansion: true` a uma das classes de armazenamento predefinidos, ou pode criar a sua própria classe de armazenamento personalizado. Note que não é suportado para reduzir o tamanho de um PVC (para evitar a perda de dados). Pode editar uma classe de armazenamento existente utilizando o `kubectl edit sc` comando. 

Por exemplo, se quiser utilizar um disco de tamanho 4 TiB, tem de criar uma classe de armazenamento que defina `cachingmode: None` porque o cache do disco não é suportado para discos [4 TiB e maiores](../virtual-machines/premium-storage-performance.md#disk-caching).

Para obter mais informações sobre aulas de armazenamento e criar a sua própria classe de armazenamento, consulte [opções de Armazenamento para aplicações em AKS.][storage-class-concepts]

Utilize o [comando de obter sc para][kubectl-get] ver as aulas de armazenamento pré-criadas. O exemplo a seguir mostra as classes de armazenamento pré-criar disponíveis num cluster AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> As reclamações de volume persistente são especificadas no GiB, mas os discos geridos a Azure são faturados pela SKU por um tamanho específico. Estes SKUs variam de 32GiB para discos S4 ou P4 a 32TiB para discos S80 ou P80 (em pré-visualização). A produção e o desempenho do IOPS de um disco gerido Premium depende tanto do SKU como do tamanho dos nós no cluster AKS. Para obter mais informações, consulte [preços e desempenho dos discos geridos.][managed-disk-pricing-performance]

## <a name="create-a-persistent-volume-claim"></a>Criar uma reivindicação de volume persistente

Uma reivindicação de volume persistente (PVC) é usada para provisões automáticas de armazenamento com base numa classe de armazenamento. Neste caso, um PVC pode usar uma das classes de armazenamento pré-criadas para criar um disco gerido a Azure standard ou premium.

Crie um ficheiro nomeado `azure-premium.yaml` e copie no seguinte manifesto. A reclamação solicita um disco com o nome `azure-managed-disk` *de 5GB* de tamanho com acesso *ReadWriteOnce.* A classe de armazenamento *com prémio gerido* é especificada como a classe de armazenamento.

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
> Para criar um disco que utilize o armazenamento padrão, use `storageClassName: default` em vez de um prémio *gerido.*

Crie a reclamação de volume persistente com o comando [de aplicação de kubectl][kubectl-apply] e especifique o seu ficheiro *azure-premium.yaml:*

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Use o volume persistente

Uma vez criada a alegação de volume persistente e o disco for provisionado com sucesso, pode criar-se um pod com acesso ao disco. O manifesto a seguir cria uma cápsula NGINX básica que utiliza a reivindicação de volume persistente chamada disco gerido por *azure* para montar o disco Azure no caminho `/mnt/azure` . Para os recipientes do Windows Server, especifique um *mountPath* utilizando a convenção do caminho do Windows, como *'D:'*.

Crie um ficheiro nomeado `azure-pvc-disk.yaml` e copie no seguinte manifesto.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Crie o casulo com o comando [de aplicação de kubectl,][kubectl-apply] como mostra o seguinte exemplo:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Agora tem uma cápsula de corrida com o seu disco Azure montado no `/mnt/azure` diretório. Esta configuração pode ser vista ao inspecionar a sua cápsula através `kubectl describe pod mypod` de , como mostra o seguinte exemplo condensado:

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

## <a name="use-ultra-disks"></a>Utilizar Discos Ultra
Para alavancar o disco ultra consulte [utilizar discos ultra no Serviço Azure Kubernetes (AKS)](use-ultra-disks.md).

## <a name="back-up-a-persistent-volume"></a>Apoiar um volume persistente

Para fazer o back up dos dados no seu volume persistente, tire uma foto do disco gerido para o volume. Em seguida, pode utilizar esta imagem para criar um disco restaurado e anexar às cápsulas como forma de restaurar os dados.

Em primeiro lugar, obtenha o nome de volume com o `kubectl get pvc` comando, tal como para o PVC nomeado *azure-gerido-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Este nome de volume forma o nome do disco Azure subjacente. Consulta para o ID do disco com [lista de discos az][az-disk-list] e fornecer o seu nome de volume de PVC, como mostrado no seguinte exemplo:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Utilize o ID do disco para criar um disco instantâneo com [a criação de instantâneo][az-snapshot-create]. O exemplo a seguir cria um instantâneo chamado *pvcSnapshot* no mesmo grupo de recursos que o cluster AKS *(MC_myResourceGroup_myAKSCluster_eastus).* Pode encontrar problemas de permissão se criar instantâneos e restaurar discos em grupos de recursos aos quais o cluster AKS não tem acesso.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Dependendo da quantidade de dados no seu disco, pode levar alguns minutos para criar o instantâneo.

## <a name="restore-and-use-a-snapshot"></a>Restaurar e usar um instantâneo

Para restaurar o disco e usá-lo com uma cápsula Kubernetes, utilize o instantâneo como fonte quando criar um disco com [disco az criar][az-disk-create]. Esta operação preserva o recurso original se precisar de aceder à imagem original dos dados. O exemplo a seguir cria um disco chamado *pvcRestored* a partir do instantâneo chamado *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Para utilizar o disco restaurado com uma vagem, especifique o ID do disco no manifesto. Obtenha a identificação do disco com o comando [do show do disco az.][az-disk-show] O exemplo a seguir obtém o ID do disco para *pvcRestored* criado no passo anterior:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Crie um manifesto de pod nomeado `azure-restored.yaml` e especifique o disco URI obtido no passo anterior. O exemplo a seguir cria um servidor web NGINX básico, com o disco restaurado montado como um volume em */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Crie o casulo com o comando [de aplicação de kubectl,][kubectl-apply] como mostra o seguinte exemplo:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Pode utilizar `kubectl describe pod mypodrestored` para ver detalhes da cápsula, como o exemplo condensado que mostra a informação de volume:

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
[premium-storage]: ../virtual-machines/disks-types.md
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
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
