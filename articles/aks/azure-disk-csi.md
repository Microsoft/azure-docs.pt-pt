---
title: Utilize controladores de interface de armazenamento de contentores (CSI) para discos Azure no Serviço Azure Kubernetes (AKS)
description: Saiba como utilizar os controladores da Interface de Armazenamento de Contentores (CSI) para Discos Azure num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422090"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Utilize os controladores da Interface de Armazenamento de Contentores de Disco Azure (CSI) no Serviço Azure Kubernetes (AKS) (pré-visualização)
O Controlador CSI disco Azure é um controlador compatível com [a especificação CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md) utilizado pela AKS para gerir o ciclo de vida dos Discos Azure. 

A Interface de Armazenamento de Contentores (CSI) é uma norma para expor sistemas arbitrários de blocos e ficheiros a cargas de trabalho contentorizadas em Kubernetes. Ao adotar e utilizar o CSI, o Azure Kubernetes Service (AKS) pode escrever, implementar e iterar plugins expondo novos ou melhorando os sistemas de armazenamento existentes em Kubernetes sem ter de tocar no código base de Kubernetes e esperar pelos seus ciclos de libertação.

Para criar um cluster AKS com suporte ao controlador CSI, consulte [ativar os controladores CSI para Discos Azure e Ficheiros Azure em AKS](csi-storage-drivers.md).

>[!NOTE]
> *"Condutores de árvores" refere-se* aos atuais condutores de armazenamento que fazem parte do código de kubernetes de núcleo vs. os novos controladores CSI que são plugins.

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>Utilize volumes persistentes csi (PV) com discos Azure 

Um [volume persistente](concepts-storage.md#persistent-volumes) representa um pedaço de armazenamento que é previsto para uso com cápsulas Kubernetes. Um volume persistente pode ser usado por uma ou muitas cápsulas, e pode ser dinamicamente ou estáticamente a provisionado. Este artigo mostra-lhe como criar dinamicamente volumes persistentes com discos Azure para uso por uma única cápsula num cluster Azure Kubernetes Service (AKS). Para o provisionamento estático, consulte [Manualmente criar e utilizar um volume com discos Azure](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Para obter mais informações sobre volumes kubernetes, consulte [as opções de Armazenamento para aplicações em AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>Criar dinâmicamente PVs de disco Azure usando as classes de armazenamento incorporado

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre as aulas de armazenamento de Kubernetes, consulte [as Classes de Armazenamento Kubernetes.][kubernetes-storage-classes] Ao utilizar o armazenamento CSI Drivers em AKS, existem 2 incorporados adicionais `StorageClasses` que alavancam **os controladores de armazenamento CSI do disco Azure**. As classes adicionais de armazenamento de CSI são criadas com o cluster ao lado das classes de armazenamento padrão na árvore.

>[!NOTE]
> *"Condutores de árvores" refere-se* aos atuais condutores de armazenamento que fazem parte do código de kubernetes do núcleo vs. condutores CSI que são plugins.

- `managed-csi` - Utiliza o armazenamento localmente redundante (LRS) da Azure StandardSSD para criar um Disco Gerido.
- `managed-csi-premium` - Utiliza o armazenamento localmente redundante (LRS) do Azure Premium para criar o Disco Gerido. 

A política de recuperação em ambas as classes de armazenamento garante que o Disco Azure subjacente é eliminado quando o respetivo volume persistente é eliminado. As aulas de armazenamento também configuram os volumes persistentes para serem expansíveis, basta editar a reivindicação de volume persistente com o novo tamanho.

Para alavancar estas classes de armazenamento, basta criar uma [Reivindicação de Volume Persistente (PVC)](concepts-storage.md#persistent-volume-claims) e respetiva vagem que as faça referências e alavancas. Uma reivindicação de volume persistente (PVC) é usada para provisões automáticas de armazenamento com base numa classe de armazenamento. Um PVC pode usar uma das classes de armazenamento pré-criadas ou uma classe de armazenamento definida pelo utilizador para criar um disco gerido pelo Azure para o SKU e tamanho desejados. Quando se cria uma definição de pod, a reivindicação de volume persistente é especificada para solicitar o armazenamento pretendido.

Crie um pod de exemplo e uma respetiva reivindicação de volume persistente com o comando [de aplicação kubectl:][kubectl-apply]

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

Pode agora validar que o disco está montado corretamente executando o comando abaixo e verificando se vê o `test.txt` ficheiro na saída: 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Criar uma classe de armazenamento personalizado

As aulas de armazenamento padrão combinam com os cenários mais comuns, mas não todos. Em alguns casos, talvez queira ter a sua própria classe de armazenamento personalizada com os seus próprios parâmetros. Para exemplificar, temos um cenário em que talvez queira mudar o `volumeBindingMode` . 

As classes de armazenamento predefinido utilizam um `volumeBindingMode: Immediate` que garante que ocorrem imediatamente após a criação do PersistentVolumeClaim. Nos casos em que as piscinas de nó são limitadas, por exemplo, utilizando Zonas de Disponibilidade, os Volumes Persistentes seriam vinculados ou a provisionados sem conhecimento dos requisitos de agendamento da Pod (neste caso, para estar em uma zona específica).

Para resolver este cenário, pode `volumeBindingMode: WaitForFirstConsumer` utilizar- que irá atrasar a ligação e provisão de um PersistenteVolume até que seja criado um Pod utilizando o PersistenteVolumeClaim. Desta forma, o PV estará em conformidade e será alotado na zona de disponibilidade (ou outra topologia) que é especificada pelas restrições de agendamento da Pod. 

Crie um ficheiro chamado `sc-azuredisk-csi-waitforfirstconsumer.yaml` , e cole o manifesto abaixo.
A classe de armazenamento é a mesma da nossa `managed-csi` aula de armazenamento, mas com uma `volumeBindingMode` outra. 

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

O controlador CSI do disco Azure suporta a criação [de instantâneos de volumes persistentes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Como parte desta capacidade, o controlador pode executar instantâneos *completos* ou [ *incrementais* ](../virtual-machines/windows/disks-incremental-snapshots.md) dependendo do valor definido no `incremental` parâmetro (por padrão é verdade). 

Para obter mais informações sobre todos os parâmetros, consulte os [parâmetros da classe Volume Snapshot](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Criar um instantâneo de volume

Para exemplificar esta capacidade, crie uma [classe instantânea de volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) com o comando de [aplicação de kubectl:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Agora vamos criar um instantâneo de [volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) a partir do PVC [que criamos dinamicamente no início deste tutorial,](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `pvc-azuredisk` .


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

Pode criar um novo PVC com base num instantâneo de volume. Use o instantâneo criado no passo anterior e crie um [novo PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) e [uma nova cápsula](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) para consumi-lo.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Finalmente, certifique-se de que é o mesmo PVC criado antes, verificando o conteúdo.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Como esperado, ainda podemos ver o nosso ficheiro previamente `test.txt` criado.

## <a name="clone-volumes"></a>Volumes de Clones

Um volume clonado é definido como uma duplicação de um volume Kubernetes existente. Para obter mais informações sobre os volumes de clonagem em Kubernetes, consulte a documentação conceptual para [a clonagem de volume.](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)

O controlador CSI da Azure Disks suporta a clonagem de volume. Para demonstrar, crie um [volume clonado](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) do [anteriormente criado](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `azuredisk-pvc` e uma nova cápsula para [consumi-lo](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Agora podemos verificar o conteúdo do volume clonado executando o abaixo e confirmando que ainda vemos o nosso `test.txt` ficheiro criado.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>Redimensionar um Volume Persistente (PV)

Em vez disso, pode solicitar um volume maior para um PVC. Edite o objeto de PVC e especifique um tamanho maior. Esta alteração desencadeia a expansão do volume subjacente que apoia o PersistenteVolume. 

> [!NOTE] 
> Um novo PersistentVolume nunca é criado para satisfazer a reivindicação. Em vez disso, um volume existente é redimensionado.

Na AKS, a classe de armazenamento incorporada `managed-csi` já permite a expansão, por isso alavancar o [PVC criado anteriormente com esta classe de armazenamento.](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) O PVC solicitou um volume persistente de 10Gi, podemos confirmar que, executando:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> Atualmente, o controlador CSI do disco Azure só suporta a redimensionamento de PVCs sem cápsulas associadas (e o volume não montado num nó específico).

Como tal, permite eliminar o pod criado anteriormente:

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

E finalmente confirmar o tamanho do PVC e dentro da cápsula: 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

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

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

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
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Contentores do Windows

O controlador CSI do disco Azure também suporta nós e contentores windows. Se desejar utilizar recipientes de janelas, siga o tutorial dos [Recipientes](windows-container-cli.md) windows para adicionar uma piscina de nó windows.

Uma vez que você tem uma piscina de nó de janelas, você pode agora apenas aproveitar as aulas de armazenamento embutido como `managed-csi` . Pode implementar um [conjunto stateful baseado em](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) exemplo por janelas que salva os picos de tempo num `data.txt` ficheiro, implantando o seguinte com o comando [de aplicação kubectl:][kubectl-apply]

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

- Para aprender a utilizar o controlador CSI para ficheiros Azure, consulte [ficheiros Use Azure com controladores CSI](azure-files-csi.md).
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