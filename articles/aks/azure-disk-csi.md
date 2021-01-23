---
title: Utilize controladores de interface de armazenamento de contentores (CSI) para discos Azure no Serviço Azure Kubernetes (AKS)
description: Saiba como utilizar os controladores da Interface de Armazenamento de Contentores (CSI) para discos Azure num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: b75f4c85831fe66158da875c21af60ee73531026
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728261"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Utilize os controladores da Interface de Armazenamento de Contentores de Disco Azure (CSI) no Serviço Azure Kubernetes (AKS) (pré-visualização)
O controlador Azure Disk Container Storage Interface (CSI) é um controlador de [especificações CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)utilizado pelo Azure Kubernetes Service (AKS) para gerir o ciclo de vida dos discos Azure.

O CSI é um padrão para expor sistemas arbitrários de blocos e ficheiros a cargas de trabalho contentorizadas em Kubernetes. Ao adotar e utilizar o CSI, a AKS pode escrever, implementar e iterar plug-ins para expor novos ou melhorar os sistemas de armazenamento existentes em Kubernetes sem ter de tocar no código de Kubernetes e esperar pelos seus ciclos de libertação.

Para criar um cluster AKS com suporte ao controlador CSI, consulte [ativar os controladores CSI para discos Azure e Ficheiros Azure em AKS](csi-storage-drivers.md).

>[!NOTE]
> *Os condutores de árvores referem-se* aos atuais condutores de armazenamento que fazem parte do código de Kubernetes contra os novos condutores do CSI, que são plug-ins.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Utilize volumes persistentes do CSI com discos Azure

Um [volume persistente](concepts-storage.md#persistent-volumes) (PV) representa um pedaço de armazenamento que é previsto para uso com cápsulas Kubernetes. Um PV pode ser usado por uma ou muitas cápsulas e pode ser alocatado dinamicamente ou estático. Este artigo mostra-lhe como criar dinâmicamente PVs com discos Azure para uso por uma única cápsula num cluster AKS. Para o provisionamento estático, consulte [Manualmente criar e utilizar um volume com discos Azure](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Para obter mais informações sobre volumes kubernetes, consulte [as opções de Armazenamento para aplicações em AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Criar dinâmicamente PVs de disco Azure utilizando as classes de armazenamento incorporado

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre as aulas de armazenamento de Kubernetes, consulte [as aulas de armazenamento de Kubernetes.][kubernetes-storage-classes] Quando utiliza os controladores CSI de armazenamento em AKS, existem dois incorporados adicionais `StorageClasses` que utilizam os controladores de armazenamento CSI do disco Azure. As classes adicionais de armazenamento de CSI são criadas com o cluster ao lado das classes de armazenamento padrão na árvore.

- `managed-csi`: Utiliza o armazenamento SSD padrão Azure localmente redundante (LRS) para criar um disco gerido.
- `managed-csi-premium`: Utiliza LRS Azure Premium para criar um disco gerido.

A política de recuperação em ambas as classes de armazenamento garante que o disco Azure subjacente é eliminado quando o respetivo PV é eliminado. As aulas de armazenamento também configuram os PVs para serem expansíveis. Basta editar a reivindicação de volume persistente (PVC) com o novo tamanho.

Para alavancar estas classes de armazenamento, crie um [PVC](concepts-storage.md#persistent-volume-claims) e respetivas cápsulas que as refira e as utilize. Um PVC é utilizado para provisões automáticas de armazenamento com base numa classe de armazenamento. Um PVC pode usar uma das classes de armazenamento pré-criadas ou uma classe de armazenamento definida pelo utilizador para criar um disco gerido pelo Azure para o SKU e tamanho desejados. Quando cria uma definição de pod, o PVC é especificado para solicitar o armazenamento pretendido.

Crie um pod de exemplo e o respetivo PVC com o comando [de aplicação kubectl:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Depois de a cápsula estar em funcionamento, crie um novo ficheiro chamado `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Pode agora validar que o disco está corretamente montado executando o seguinte comando e verificando que vê o `test.txt` ficheiro na saída:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Criar uma classe de armazenamento personalizado

As aulas de armazenamento padrão combinam com os cenários mais comuns, mas não todos. Em alguns casos, talvez queira ter a sua própria classe de armazenamento personalizada com os seus próprios parâmetros. Por exemplo, temos um cenário em que talvez queira mudar a `volumeBindingMode` classe.

As classes de armazenamento predefinido utilizam uma `volumeBindingMode: Immediate` classe que garante que ocorre imediatamente após a criação do PVC. Nos casos em que as piscinas de nó são limitadas, por exemplo, utilizando zonas de disponibilidade, os PVs seriam vinculados ou alobrados sem conhecimento dos requisitos de agendamento da cápsula (neste caso, para estar em uma zona específica).

Para resolver este cenário, pode utilizar `volumeBindingMode: WaitForFirstConsumer` - o que atrasa a ligação e o provisionamento de um PV até que seja criado um casulo que utiliza o PVC. Desta forma, o PV estará em conformidade e será alotado na zona de disponibilidade (ou outra topologia) que é especificada pelas restrições de agendamento da cápsula.

Crie um ficheiro chamado `sc-azuredisk-csi-waitforfirstconsumer.yaml` , e cole o seguinte manifesto.
A aula de armazenamento é a mesma da nossa `managed-csi` aula de armazenamento, mas com uma `volumeBindingMode` classe diferente.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Crie a classe de armazenamento com o comando [de aplicação de kubectl][kubectl-apply] e especifique o seu `sc-azuredisk-csi-waitforfirstconsumer.yaml` ficheiro:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Instantâneos de volume

O controlador CSI do disco Azure suporta a criação [de instantâneos de volumes persistentes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Como parte desta capacidade, o controlador pode executar instantâneos *completos* ou [ *incrementais*](../virtual-machines/disks-incremental-snapshots.md) dependendo do valor definido no `incremental` parâmetro (por padrão, é verdade).

Para obter detalhes sobre todos os parâmetros, consulte os [parâmetros da classe snapshot de volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Criar um instantâneo de volume

Para um exemplo desta capacidade, crie uma [classe instantânea de volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) com o comando de [aplicação de kubectl:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Agora vamos criar um instantâneo de [volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) a partir do PVC que [criamos dinamicamente no início deste tutorial,](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Verifique se o instantâneo foi criado corretamente:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Criar um novo PVC baseado num instantâneo de volume

Pode criar um novo PVC com base num instantâneo de volume. Use o instantâneo criado no passo anterior e crie um [novo PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) e uma [nova cápsula](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) para consumi-lo.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Finalmente, vamos certificar-nos de que é o mesmo PVC criado antes, verificando o conteúdo.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Como esperado, ainda podemos ver o nosso ficheiro previamente `test.txt` criado.

## <a name="clone-volumes"></a>Volumes de clones

Um volume clonado é definido como uma duplicação de um volume kubernetes existente. Para obter mais informações sobre os volumes de clonagem em Kubernetes, consulte a documentação conceptual para [a clonagem de volume.](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)

O controlador CSI dos discos Azure suporta a clonagem de volume. Para demonstrar, crie um [volume clonado](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) do [anteriormente criado](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` e uma nova cápsula para [consumi-lo](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Agora podemos verificar o conteúdo do volume clonado executando o seguinte comando e confirmando que ainda vemos o nosso `test.txt` ficheiro criado.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Redimensionar um volume persistente

Em vez disso, pode solicitar um volume maior para um PVC. Editar o objeto de PVC e especificar um tamanho maior. Esta alteração desencadeia a expansão do volume subjacente que apoia o PV.

> [!NOTE]
> Um novo PV nunca é criado para satisfazer a reivindicação. Em vez disso, um volume existente é redimensionado.

Na AKS, a classe de armazenamento incorporada `managed-csi` já permite a expansão, por isso use o [PVC criado anteriormente com esta classe de armazenamento.](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) O PVC solicitou um volume persistente de 10 Gi. Podemos confirmar isso correndo:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Atualmente, o controlador CSI do disco Azure só suporta a redimensionamento de PVCs sem cápsulas associadas (e o volume não montado num nó específico).

Como tal, vamos apagar o pod que criamos anteriormente:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Vamos expandir o PVC aumentando o `spec.resources.requests.storage` campo:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Vamos confirmar que o volume é agora maior:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> O PVC não refletirá o novo tamanho até ter uma cápsula associada a ele novamente.

Vamos criar uma nova cápsula:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

E, finalmente, confirmar o tamanho do PVC e dentro da cápsula:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Disco compartilhado

[Os discos partilhados Azure](../virtual-machines/disks-shared.md) são uma funcionalidade de discos geridos azure que permite anexar um disco Azure aos nós de agente simultaneamente. A anexação de um disco gerido a múltiplos nós de agente permite,por exemplo, implantar novas ou migrar aplicações agrupadas existentes para o Azure.

> [!IMPORTANT] 
> Atualmente, apenas o dispositivo de bloco bruto `volumeMode: Block` é suportado pelo controlador CSI do disco Azure. As aplicações devem gerir a coordenação e controlo de escritas, leituras, fechaduras, caches, suportes e esgrima no disco partilhado, que está exposto como um dispositivo de bloco cru.

Vamos criar um ficheiro chamado `shared-disk.yaml` copiando o seguinte comando que contém a classe de armazenamento de discos partilhados e PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Crie a classe de armazenamento com o comando [de aplicação de kubectl][kubectl-apply] e especifique o seu `shared-disk.yaml` ficheiro:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Agora vamos criar um ficheiro chamado `deployment-shared.yml` copiando o seguinte comando:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Crie a implementação com o comando [de aplicação de kubectl][kubectl-apply] e especifique o seu `deployment-shared.yml` ficheiro:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finalmente, vamos verificar o dispositivo de bloqueio dentro da cápsula:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Contentores do Windows

O controlador CSI do disco Azure também suporta nós e contentores windows. Se pretender utilizar recipientes Windows, siga o tutorial dos [recipientes windows](windows-container-cli.md) para adicionar uma piscina de nó windows.

Depois de ter uma piscina de nó windows, pode agora utilizar as aulas de armazenamento incorporadas como `managed-csi` . Pode implementar um [conjunto stateful baseado no Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) que guarda os picos de tempo no `data.txt` ficheiro, implantando o seguinte comando com o comando [de aplicação kubectl:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Pode agora validar o conteúdo do volume executando:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Próximos passos

- Para aprender a utilizar controladores CSI para ficheiros Azure, consulte [Use Azure Files com controladores CSI](azure-files-csi.md).
- Para obter mais informações sobre as melhores práticas de armazenamento, consulte [as melhores práticas de armazenamento e backups no Serviço Azure Kubernetes.][operator-best-practices-storage]


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register