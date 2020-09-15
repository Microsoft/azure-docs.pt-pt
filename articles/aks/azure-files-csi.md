---
title: Utilize controladores de interface de armazenamento de contentores (CSI) para ficheiros Azure no Serviço Azure Kubernetes (AKS)
description: Saiba como utilizar os controladores da Interface de Armazenamento de Contentores (CSI) para ficheiros Azure num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 330c1b74a46b0f18af1068797d080e903f516ea6
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089875"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Utilize controladores da Interface de Armazenamento de Contentores de Ficheiros Azure (CSI) no Serviço Azure Kubernetes (AKS) (pré-visualização)

O controlador Azure Files Container Storage Interface (CSI) é um controlador de [especificações CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)utilizado pelo Azure Kubernetes Service (AKS) para gerir o ciclo de vida das ações da Azure Files.

O CSI é um padrão para expor sistemas arbitrários de blocos e ficheiros a cargas de trabalho contentorizadas em Kubernetes. Ao adotar e utilizar o CSI, a AKS pode agora escrever, implementar e iterar plug-ins para expor novos ou melhorar os sistemas de armazenamento existentes em Kubernetes sem ter de tocar no código de Kubernetes e esperar pelos seus ciclos de libertação.

Para criar um cluster AKS com suporte ao controlador CSI, consulte [ativar os controladores CSI para discos Azure e Ficheiros Azure em AKS](csi-storage-drivers.md).

>[!NOTE]
> *Os condutores de árvores referem-se* aos atuais condutores de armazenamento que fazem parte do código de Kubernetes contra os novos condutores do CSI, que são plug-ins.

## <a name="use-a-persistent-volume-with-azure-files"></a>Utilize um volume persistente com ficheiros Azure

Um [volume persistente (PV)](concepts-storage.md#persistent-volumes) representa um pedaço de armazenamento que é previsto para uso com cápsulas Kubernetes. Um PV pode ser usado por uma ou muitas cápsulas e pode ser alocatado dinamicamente ou estático. Se várias cápsulas precisarem de acesso simultâneo ao mesmo volume de armazenamento, pode utilizar ficheiros Azure para se ligar utilizando o protocolo Bloco de Mensagens do [Servidor (SMB).][smb-overview] Este artigo mostra-lhe como criar dinamicamente uma partilha de Ficheiros Azure para utilização por várias cápsulas num cluster AKS. Para o provisionamento estático, consulte [manualmente criar e utilizar um volume com uma partilha de Ficheiros Azure](azure-files-volume.md).

Para obter mais informações sobre volumes kubernetes, consulte [as opções de Armazenamento para aplicações em AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Criar dinâmicamente PVs de Ficheiros Azure utilizando as classes de armazenamento incorporado

Uma classe de armazenamento é usada para definir como uma partilha de Ficheiros Azure é criada. Uma conta de armazenamento é criada automaticamente no [grupo de recursos de nó][node-resource-group] para utilização com a classe de armazenamento para deter as ações do Azure Files. Escolha um dos [seguintes SKUs de redundância de armazenamento Azure][storage-skus] para *o skuName:*

* **Standard_LRS**: Armazenamento localmente redundante padrão
* **Standard_GRS**: Armazenamento geo-redundante padrão
* **Standard_ZRS**: Armazenamento normal de zona redundante
* **Standard_RAGRS**: Armazenamento geo-redundante de acesso de leitura padrão
* **Premium_LRS**: Armazenamento premium localmente redundante

> [!NOTE]
> A Azure Files suporta o Azure Premium Storage. A quota mínima de ficheiros premium é de 100 GB.

Quando utiliza os controladores CSI de armazenamento em AKS, existem dois incorporados adicionais `StorageClasses` que utilizam os controladores de armazenamento CSI Azure Files. As classes adicionais de armazenamento de CSI são criadas com o cluster ao lado das classes de armazenamento padrão na árvore.

- `azurefile-csi`: Utiliza o Azure Standard Storage para criar uma partilha de Ficheiros Azure.
- `azurefile-csi-premium`: Utiliza o Azure Premium Storage para criar uma partilha de Ficheiros Azure.

A política de recuperação de ambas as classes de armazenamento garante que a partilha de Ficheiros Azure subjacentes seja eliminada quando o respetivo PV é eliminado. As aulas de armazenamento também configuram as ações de ficheiro para serem expansíveis, basta editar a reivindicação persistente de volume (PVC) com o novo tamanho.

Para utilizar estas aulas de armazenamento, crie um [PVC](concepts-storage.md#persistent-volume-claims) e uma cápsula respetivo que as refira e as utilize. Um PVC é utilizado para provisões automáticas de armazenamento com base numa classe de armazenamento. Um PVC pode usar uma das classes de armazenamento pré-criadas ou uma classe de armazenamento definida pelo utilizador para criar uma partilha de Ficheiros Azure para o SKU e tamanho desejados. Quando cria uma definição de pod, o PVC é especificado para solicitar o armazenamento pretendido.

Crie um [exemplo de PVC e pod `outfile` que imprime a data atual num](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) comando de [aplicação de kubectl:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Depois de a cápsula estar no estado de funcionamento, pode validar que a partilha de ficheiros está corretamente montada executando o seguinte comando e verificando se a saída `outfile` contém:

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Criar uma classe de armazenamento personalizado

As aulas de armazenamento padrão combinam com os cenários mais comuns, mas não todos. Em alguns casos, talvez queira ter a sua própria classe de armazenamento personalizada com os seus próprios parâmetros. Por exemplo, utilize o seguinte manifesto para configurar a `mountOptions` parte do ficheiro.

O valor predefinido para *ficheirosMode* e *dirMode* é *0777* para as ações de ficheiros montadas em Kubernetes. Pode especificar as diferentes opções de montagem no objeto da classe de armazenamento.

Crie um ficheiro com o nome `azure-file-sc.yaml` de , e cole o seguinte exemplo manifesto:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Crie a classe de armazenamento com o [comando de aplicação de kubectl:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

O controlador CSI Azure Files suporta a criação [de instantâneos de volumes persistentes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) e as partilhas de ficheiros subjacentes.

Crie uma [classe instantânea de volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) com o comando de [aplicação de kubectl:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Crie um instantâneo de [volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) a partir do PVC [que criamos dinamicamente no início deste tutorial,](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Verifique se o instantâneo foi criado corretamente:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>Redimensionar um volume persistente

Pode solicitar um volume maior para um PVC. Editar o objeto de PVC e especificar um tamanho maior. Esta alteração desencadeia a expansão do volume subjacente que apoia o PV.

> [!NOTE]
> Um novo PV nunca é criado para satisfazer a reivindicação. Em vez disso, um volume existente é redimensionado.

Na AKS, a classe de armazenamento incorporada `azurefile-csi` já suporta a expansão, por isso use o [PVC criado anteriormente com esta classe de armazenamento.](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) O PVC solicitou uma partilha de ficheiros 100Gi. Podemos confirmar isso correndo:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Expandir o PVC aumentando o `spec.resources.requests.storage` campo:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Verifique se tanto o PVC como o sistema de ficheiros dentro da cápsula mostram o novo tamanho:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="windows-containers"></a>Contentores do Windows

O controlador CSI ficheiros Azure também suporta nós e contentores windows. Se pretender utilizar recipientes Windows, siga o tutorial de [contentores](windows-container-cli.md) do Windows para adicionar uma piscina de nó windows.

Depois de ter uma piscina de nó windows, use as aulas de armazenamento incorporadas ou crie as `azurefile-csi` personalizadas. Pode implementar um [conjunto stateful baseado no Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) que guarda os picos de tempo num `data.txt` ficheiro, implantando o seguinte comando com o comando [de aplicação kubectl:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Validar o conteúdo do volume executando:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Passos seguintes

- Para aprender a utilizar os controladores CSI para discos Azure, consulte [discos Azure use com controladores CSI](azure-disk-csi.md).
- Para obter mais informações sobre as melhores práticas de armazenamento, consulte [as melhores práticas de armazenamento e backups no Serviço Azure Kubernetes.][operator-best-practices-storage]


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md