---
title: Ativar o suporte ultra disco no serviço Azure Kubernetes (AKS)
description: Saiba como ativar e configurar Discos Ultra num cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: c743162ed3f75386287e050443e82069e797ced9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502574"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Utilize discos ultra Azure no Serviço Azure Kubernetes (pré-visualização)

[Os discos Azure ultra](../virtual-machines/disks-enable-ultra-ssd.md) oferecem alta produção, alta produção de IOPS e armazenamento consistente de disco de baixa latência para as suas aplicações imponentes. Um dos principais benefícios dos discos ultra é a capacidade de alterar dinamicamente o desempenho do SSD juntamente com as suas cargas de trabalho sem a necessidade de reiniciar os nós de agente. Os discos ultra são adequados para cargas de trabalho intensivas de dados.

## <a name="before-you-begin"></a>Antes de começar

Esta funcionalidade só pode ser definida no tempo de criação de cluster ou de criação de piscinas de nó.

> [!IMPORTANT]
> Os discos ultra Azure requerem nodepools implantados em zonas de disponibilidade e regiões que suportam estes discos, bem como apenas séries VM específicas. Consulte o [**âmbito e limitações ga dos discos Ultra**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="register-the-enableultrassd-preview-feature"></a>Registar a `EnableUltraSSD` funcionalidade de pré-visualização

Para criar um cluster AKS ou um conjunto de nós que possa alavancar discos Ultra, tem de ativar a bandeira de `EnableUltraSSD` funcionalidades na sua subscrição.

Registe a bandeira de `EnableUltraSSD` características utilizando o comando [de registo de recurso az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Demora alguns minutos para que o estado seja *apresentado.* Pode verificar o estado de registo utilizando o comando [da lista de recursos az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando [de registo do fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS ou um conjunto de nós que possa usar Discos Ultra, você precisa da mais recente extensão CLI *de pré-visualização de aks.* Instale a extensão Azure CLI *de pré-visualização aks* utilizando o comando [de adicionar extensão az][az-extension-add] ou instale quaisquer atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Limitações
- Consulte o [ **âmbito e limitações ga dos discos Ultra**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- A gama de tamanho suportado para um disco Ultra está entre 100 e 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Criar um novo cluster que possa usar discos Ultra

Crie um cluster AKS capaz de alavancar discos ultra usando os seguintes comandos CLI. Utilize a `--aks-custom-headers` bandeira para definir a `EnableUltraSSD` função.

Criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Crie o cluster AKS com suporte para Discos Ultra.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Se pretender criar clusters sem suporte ultra disco, pode fazê-lo omitindo o `--aks-custom-headers` parâmetro personalizado.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Ativar discos ultra num cluster existente

Pode ativar discos ultra em clusters existentes adicionando uma nova piscina de nós ao seu cluster que suporta discos ultra. Configure uma nova piscina de nós para utilizar discos ultra usando a `--aks-custom-headers` bandeira.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Se pretender criar novos conjuntos de nós sem suporte para discos ultra, pode fazê-lo omitindo o `--aks-custom-headers` parâmetro personalizado.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Use discos ultra dinamicamente com uma classe de armazenamento

Para utilizar discos ultra em nossas implementações ou conjuntos imponentes, pode utilizar uma [classe de armazenamento para provisões dinâmicas](azure-disks-dynamic-pv.md).

### <a name="create-the-storage-class"></a>Criar a classe de armazenamento

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre as aulas de armazenamento de Kubernetes, consulte [as Classes de Armazenamento Kubernetes.][kubernetes-storage-classes]

Neste caso, vamos criar uma classe de armazenamento que faz referência a discos ultra. Crie um ficheiro nomeado `azure-ultra-disk-sc.yaml` e copie no seguinte manifesto.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Crie a classe de armazenamento com o comando [de aplicação de kubectl][kubectl-apply] e especifique o seu ficheiro *azure-ultra-disco-sc.yaml:*

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Criar uma reivindicação de volume persistente

Uma reivindicação de volume persistente (PVC) é usada para provisões automáticas de armazenamento com base numa classe de armazenamento. Neste caso, um PVC pode usar a classe de armazenamento previamente criada para criar um disco ultra.

Crie um ficheiro nomeado `azure-ultra-disk-pvc.yaml` e copie no seguinte manifesto. A reclamação solicita um disco com o nome `ultra-disk` *de 1000 GB* de tamanho com acesso *ReadWriteOnce.* A classe de armazenamento *ultra-disco-sc* é especificada como a classe de armazenamento.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Crie a reivindicação de volume persistente com o comando [de aplicação de kubectl][kubectl-apply] e especifique o seu ficheiro *azure-ultra-disco-pvc.yaml:*

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Use o volume persistente

Uma vez criada a alegação de volume persistente e o disco for provisionado com sucesso, pode criar-se um pod com acesso ao disco. O manifesto a seguir cria uma cápsula NGINX básica que utiliza a reivindicação de volume persistente chamada *ultra-disco* para montar o disco Azure no caminho `/mnt/azure` .

Crie um ficheiro nomeado `nginx-ultra.yaml` e copie no seguinte manifesto.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
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
        claimName: ultra-disk
```

Crie o casulo com o comando [de aplicação de kubectl,][kubectl-apply] como mostra o seguinte exemplo:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Agora tem uma cápsula de corrida com o seu disco Azure montado no `/mnt/azure` diretório. Esta configuração pode ser vista ao inspecionar a sua cápsula através `kubectl describe pod nginx-ultra` de , como mostra o seguinte exemplo condensado:

```console
$ kubectl describe pod nginx-ultra

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


## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre discos ultra, consulte [o Uso de discos ultra Azure](../virtual-machines/disks-enable-ultra-ssd.md).
- Para obter mais informações sobre as melhores práticas de armazenamento, consulte [as melhores práticas de armazenamento e backups no Serviço Azure Kubernetes (AKS)][operator-best-practices-storage]

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
