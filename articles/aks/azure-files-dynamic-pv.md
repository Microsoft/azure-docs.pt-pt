---
title: Criar dinamicamente um volume de ficheiros para vários pods no Azure Kubernetes Service (AKS)
description: Saiba como criar dinamicamente um volume persistente com ficheiros do Azure para utilização com vários pods em simultâneo no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 43f3a55bc820a232ccebc3a940faa86f9eb730f7
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338268"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dinamicamente criar e utilizar um volume persistente com ficheiros do Azure no Azure Kubernetes Service (AKS)

Um volume persistente representa uma parte do armazenamento aprovisionado para utilização com pods do Kubernetes. Um volume persistente pode ser utilizado por um ou vários pods e pode ser dinamicamente ou estaticamente aprovisionado. Se precisam de vários pods acesso simultâneo no mesmo volume de armazenamento, pode utilizar ficheiros do Azure para ligar através da [protocolo Server Message Block (SMB)][smb-overview]. Este artigo mostra-lhe como criar dinamicamente uma partilha de ficheiros do Azure para utilização por vários pods num cluster do Azure Kubernetes Service (AKS).

Para obter mais informações sobre volumes do Kubernetes, consulte [opções de armazenamento para aplicações no AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Criar uma classe de armazenamento

Uma classe de armazenamento é utilizada para definir como uma partilha de ficheiros do Azure é criada. Uma conta de armazenamento é criada automaticamente no *_MC* grupo de recursos para utilização com a classe de armazenamento para armazenar as partilhas de ficheiros do Azure. Escolha dos seguintes procedimentos [redundância de armazenamento do Azure] [ storage-skus] para *skuName*:

* *Standard_LRS* -standard armazenamento localmente redundante (LRS)
* *Standard_GRS* -standard armazenamento georredundante (GRS)
* *Standard_RAGRS* -armazenamento geograficamente redundante com padrão de acesso de leitura (RA-GRS)

> [!NOTE]
> Ficheiros do Azure atualmente funciona apenas com o armazenamento Standard. Se utilizar o armazenamento Premium, o volume não consegue aprovisionar.

Para obter mais informações sobre classes de armazenamento do Kubernetes para ficheiros do Azure, consulte [Classes de armazenamento do Kubernetes][kubernetes-storage-classes].

Crie um ficheiro denominado `azure-file-sc.yaml` e copie o manifesto de exemplo seguinte. Para obter mais informações sobre *mountOptions*, consulte a [opções de montagem] [ mount-options] secção.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Criar a classe de armazenamento com o [aplicam-se de kubectl] [ kubectl-apply] comando:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Criar uma função de cluster e a ligação

Clusters do AKS utilizar o controlo de acesso baseado em funções (RBAC) Kubernetes para ações de limite que podem ser executadas. *Funções* definir as permissões para conceder, e *enlaces* aplicá-las para os usuários desejados. Estas atribuições podem ser aplicadas a um determinado espaço de nomes ou em todo o cluster. Para obter mais informações, consulte [autorização RBAC usando][kubernetes-rbac].

Para permitir que a plataforma do Azure criar os recursos de armazenamento necessário, crie uma *ClusterRole* e *ClusterRoleBinding*. Crie um ficheiro denominado `azure-pvc-roles.yaml` e copie o YAML seguinte:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Atribuir as permissões com o [aplicam-se de kubectl] [ kubectl-apply] comando:

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Criar uma afirmação de volume persistente

Uma afirmação de volume persistente (PVC) usa o objeto de classe de armazenamento para provisionar dinamicamente uma partilha de ficheiros do Azure. O YAML seguinte pode ser utilizado para criar uma afirmação de volume persistente *5GB* tamanho com *ReadWriteMany* acesso. Para obter mais informações sobre os modos de acesso, consulte a [volume persistente do Kubernetes] [ access-modes] documentação.

Agora, crie um ficheiro denominado `azure-file-pvc.yaml` e copie o YAML seguinte. Certifique-se de que o *storageClassName* corresponde a classe de armazenamento criada no último passo:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Criar a afirmação de volume persistente com o [aplicam-se de kubectl] [ kubectl-apply] comando:

```console
kubectl apply -f azure-file-pvc.yaml
```

Depois de concluído, será criada a partilha de ficheiros. Segredo do Kubernetes, também é criado que inclui informações de ligação e credenciais. Pode utilizar o [kubectl Obtenha] [ kubectl-get] comando para ver o estado do PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Utilizar o volume persistente

O YAML seguinte cria um pod que utiliza a afirmação de volume persistente *azurefile* montar a partilha de ficheiros do Azure a */mnt/azure* caminho.

Crie um ficheiro denominado `azure-pvc-files.yaml`e copie o YAML seguinte. Certifique-se de que o *claimName* corresponde ao PVC criado no último passo.

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
        claimName: azurefile
```

Criar o pod com o [aplicam-se de kubectl] [ kubectl-apply] comando.

```console
kubectl apply -f azure-pvc-files.yaml
```

Tem agora um pod em execução com a partilha de ficheiros do Azure montada no */mnt/azure* diretório. Esta configuração pode ser vista quando inspecionar o seu pod via `kubectl describe pod mypod`. O resultado de exemplo condensado seguinte mostra o volume montado no contentor:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
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
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opções de montagem

Predefinido *fileMode* e *dirMode* valores diferentes entre versões do Kubernetes, conforme descrito na tabela seguinte.

| versão | valor |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 ou superior | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 ou superior | 0755 |

Se utilizar um cluster de versão 1.8.5 ou superior e criar dinamicamente o volume persistente com uma classe de armazenamento, opções de montagem podem ser especificadas no objeto de classe de armazenamento. O exemplo seguinte define *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Se utilizar um cluster de versão 1.8.0 - 1.8.4, um contexto de segurança pode ser especificado com o *runAsUser* valor definido como *0*. Para obter mais informações sobre o contexto de segurança de Pod, consulte [configurar um contexto de segurança][kubernetes-security-context].

## <a name="next-steps"></a>Passos Seguintes

Para as práticas recomendadas associadas, consulte [melhores práticas para o armazenamento e cópias de segurança no AKS][operator-best-practices-storage].

Saiba mais sobre os volumes de persistentes Kubernetes com ficheiros do Azure.

> [!div class="nextstepaction"]
> [Plug-in do Kubernetes para ficheiros do Azure][kubernetes-files]

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
