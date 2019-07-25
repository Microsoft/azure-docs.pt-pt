---
title: Criar um volume estático para pods no serviço de kubernetes do Azure (AKS)
description: Saiba como criar um volume manualmente com discos do Azure para uso com um pod no serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 9017c8cf721fbb9c493dc18da769b9d6e83ddf05
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "67616140"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Criar e usar manualmente um volume com discos do Azure no serviço kubernetes do Azure (AKS)

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Se um único Pod precisar de acesso ao armazenamento, você poderá usar discos do Azure para apresentar um volume nativo para uso do aplicativo. Este artigo mostra como criar um disco do Azure manualmente e anexá-lo a um pod no AKS.

> [!NOTE]
> Um disco do Azure só pode ser montado em um único pod de cada vez. Se você precisar compartilhar um volume persistente em vários pods, use [os arquivos do Azure][azure-files-volume].

Para obter mais informações sobre volumes kubernetes, consulte [Opções de armazenamento para aplicativos em AKs][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Criar um disco do Azure

Ao criar um disco do Azure para uso com o AKS, você pode criar o recurso de disco no grupo de recursos do **nó** . Essa abordagem permite que o cluster AKS acesse e gerencie o recurso de disco. Se, em vez disso, você criar o disco em um grupo de recursos separado, deverá conceder à entidade de serviço do AKS (serviço kubernetes `Contributor` do Azure) para o cluster a função para o grupo de recursos do disco.

Para este artigo, crie o disco no grupo de recursos do nó. Primeiro, obtenha o nome do grupo de recursos com o comando [AZ AKs show][az-aks-show] e `--query nodeResourceGroup` adicione o parâmetro de consulta. O exemplo a seguir obtém o grupo de recursos do nó para o nome do cluster AKS *myAKSCluster* no nome do grupo de recursos MyResource Group:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora, crie um disco usando o comando [AZ Disk Create][az-disk-create] . Especifique o nome do grupo de recursos do nó obtido no comando anterior e, em seguida, um nome para o recurso de disco, como *myAKSDisk*. O exemplo a seguir cria um disco de *20*GiB e gera a ID do disco uma vez criado. Se você precisar criar um disco para uso com contêineres do Windows Server (atualmente em visualização no AKs), adicione `--os-type windows` o parâmetro para formatar corretamente o disco.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Os discos do Azure são cobrados pelo SKU por um tamanho específico. Essas SKUs variam de 32GiB para discos S4 ou P4 para 32TiB para discos S80 ou P80 (em versão prévia). O desempenho de taxa de transferência e IOPS de um disco gerenciado Premium depende tanto da SKU quanto do tamanho da instância dos nós no cluster AKS. Consulte o [preço e o desempenho de Managed disks][managed-disk-pricing-performance].

A ID de recurso de disco é exibida depois que o comando for concluído com êxito, conforme mostrado na saída de exemplo a seguir. Essa ID de disco é usada para montar o disco na próxima etapa.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montar disco como volume

Para montar o disco do Azure em seu Pod, configure o volume na especificação do contêiner. Crie um novo arquivo chamado `azure-disk-pod.yaml` com o conteúdo a seguir. Atualize `diskName` com o nome do disco criado na etapa anterior e `diskURI` com a ID do disco mostrada na saída do comando disco criar. Se desejar, atualize o `mountPath`, que é o caminho no qual o disco do Azure está montado no pod. Para contêineres do Windows Server (atualmente em visualização em AKS), especifique um *mountPath* usando a Convenção de caminho do Windows, como *: '* .

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

Use o `kubectl` comando para criar o pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Agora você tem um pod em execução com um disco do Azure `/mnt/azure`montado em. Você pode usar `kubectl describe pod mypod` o para verificar se o disco foi montado com êxito. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

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

## <a name="next-steps"></a>Passos Seguintes

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para armazenamento e backups em AKs][operator-best-practices-storage].

Para obter mais informações sobre os clusters AKS interagem com discos do Azure, consulte o [plug-in kubernetes para discos do Azure][kubernetes-disks].

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
