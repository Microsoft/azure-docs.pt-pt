---
title: Criar dinamicamente a partilha de Ficheiros Azure
titleSuffix: Azure Kubernetes Service
description: Aprenda a criar dinamicamente um volume persistente com ficheiros Azure para utilização com várias cápsulas simultâneas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 2ad2affee34348e8c2fc7b734c8b49d0aec8db40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744914"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Criar e utilizar dinamicamente um volume persistente com ficheiros Azure no Serviço Azure Kubernetes (AKS)

Um volume persistente representa um pedaço de armazenamento que foi previsto para ser usado com cápsulas Kubernetes. Um volume persistente pode ser usado por uma ou muitas cápsulas, e pode ser dinamicamente ou estáticamente a provisionado. Se várias cápsulas precisarem de acesso simultâneo ao mesmo volume de armazenamento, pode utilizar ficheiros Azure para se ligar utilizando o protocolo Bloco de Mensagens do [Servidor (SMB).][smb-overview] Este artigo mostra-lhe como criar dinamicamente uma partilha de Ficheiros Azure para utilização por várias cápsulas num cluster Azure Kubernetes Service (AKS).

Para obter mais informações sobre volumes kubernetes, consulte [as opções de Armazenamento para aplicações em AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="create-a-storage-class"></a>Criar uma classe de armazenamento

Uma classe de armazenamento é usada para definir como uma partilha de ficheiros Azure é criada. Uma conta de armazenamento é criada automaticamente no [grupo de recursos de nó][node-resource-group] para utilização com a classe de armazenamento para deter as ações de ficheiroS Azure. Escolha a seguinte [redundância de armazenamento Azure][storage-skus] para *o skuName:*

* *Standard_LRS* - armazenamento localmente redundante padrão (LRS)
* *Standard_GRS* - armazenamento geo-redundante padrão (GRS)
* *Standard_ZRS* - armazenamento redundante de zona padrão (ZRS)
* *Standard_RAGRS* - armazenamento geo-redundante de acesso de leitura padrão (RA-GRS)
* *Premium_LRS* - armazenamento premium localmente redundante (LRS)
* *Premium_ZRS* - armazenamento redundante de zona premium (ZRS)

> [!NOTE]
> Azure Files suporta armazenamento premium em clusters AKS que executam Kubernetes 1.13 ou superior, a quota de ficheiros premium mínimo é de 100GB

Para obter mais informações sobre as aulas de armazenamento de Kubernetes para Ficheiros Azure, consulte [as Classes de Armazenamento Kubernetes][kubernetes-storage-classes].

Crie um ficheiro com o nome `azure-file-sc.yaml` e copie no manifesto de exemplo a seguir. Para obter mais informações sobre *as opções mountOptions,* consulte a secção [de opções Mount.][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

Crie a classe de armazenamento com o [comando de aplicação de kubectl:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Criar uma reivindicação de volume persistente

Uma reivindicação de volume persistente (PVC) utiliza o objeto da classe de armazenamento para provisão dinamicamente uma partilha de ficheiros Azure. O YAML seguinte pode ser usado para criar uma reivindicação persistente de volume *de 5 GB* de tamanho com acesso *ReadWriteMany.* Para obter mais informações sobre os modos de acesso, consulte a documentação de volume persistente dos [Kubernetes.][access-modes]

Agora crie um ficheiro nomeado `azure-file-pvc.yaml` e copie no YAML seguinte. Certifique-se de que o *storageClassName* corresponde à classe de armazenamento criada no último passo:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Se utilizar a *Premium_LRS* sku para a sua classe de armazenamento, o valor mínimo para *armazenamento* deve ser *100Gi*.

Crie a reivindicação de volume persistente com o comando [de aplicação de kubectl:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

Uma vez concluída, a partilha de ficheiros será criada. Um segredo de Kubernetes também é criado que inclui informações de conexão e credenciais. Pode utilizar o [comando de obter kubectl][kubectl-get] para visualizar o estado do PVC:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Use o volume persistente

O YAML seguinte cria uma cápsula que utiliza o volume persistente que reclama *o meu-azurefile* para montar a partilha de ficheiros Azure no caminho */mnt/azure.* Para os recipientes do Windows Server, especifique um *mountPath* utilizando a convenção do caminho do Windows, como *'D:'*.

Crie um ficheiro nomeado `azure-pvc-files.yaml` , e copie no seguinte YAML. Certifique-se de que o *nome de reclamação* corresponde ao PVC criado no último passo.

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
        claimName: my-azurefile
```

Crie a cápsula com o [comando de aplicação de kubectl.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

Tem agora uma cápsula de corrida com a sua partilha Azure Files montada no diretório */mnt/azure.* Esta configuração pode ser vista ao inspecionar a sua cápsula através `kubectl describe pod mypod` de . A seguinte saída de exemplo condensada mostra o volume montado no recipiente:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opções de montagem

O valor predefinido para *ficheirosMode* e *dirMode* é *0777* para a versão 1.13.0 de Kubernetes ou superior. Se criar dinamicamente o volume persistente com uma classe de armazenamento, as opções de montagem podem ser especificadas no objeto da classe de armazenamento. O exemplo a seguir define *0777:*

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Passos seguintes

Para as melhores práticas associadas, consulte [as melhores práticas de armazenamento e backups em AKS][operator-best-practices-storage].

Para parâmetros de classe de armazenamento, consulte [a Provisão Dinâmica](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision).

Saiba mais sobre os volumes persistentes da Kubernetes utilizando ficheiros Azure.

> [!div class="nextstepaction"]
> [Plugin kubernetes para ficheiros Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
