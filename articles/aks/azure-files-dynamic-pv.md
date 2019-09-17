---
title: Criar dinamicamente um volume de arquivos para vários pods no serviço kubernetes do Azure (AKS)
description: Saiba como criar dinamicamente um volume persistente com arquivos do Azure para uso com vários pods simultâneos no AKS (serviço kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 09/12/2019
ms.author: mlearned
ms.openlocfilehash: 045fcb3286c89097459a4a8405d22ee70e44c205
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018826"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Criar e usar dinamicamente um volume persistente com arquivos do Azure no serviço kubernetes do Azure (AKS)

Um volume persistente representa uma parte do armazenamento que foi provisionado para uso com kubernetes pods. Um volume persistente pode ser usado por um ou vários pods e pode ser provisionado de forma dinâmica ou estática. Se vários pods precisarem de acesso simultâneo ao mesmo volume de armazenamento, você poderá usar os arquivos do Azure para se conectar usando o [protocolo SMB][smb-overview]. Este artigo mostra como criar dinamicamente um compartilhamento de arquivos do Azure para uso por vários pods em um cluster AKS (serviço de kubernetes do Azure).

Para obter mais informações sobre volumes kubernetes, consulte [Opções de armazenamento para aplicativos em AKs][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Criar uma classe de armazenamento

Uma classe de armazenamento é usada para definir como um compartilhamento de arquivos do Azure é criado. Uma conta de armazenamento é criada automaticamente no [grupo de recursos do nó][node-resource-group] para uso com a classe de armazenamento para manter os compartilhamentos de arquivos do Azure. Escolha a seguinte [redundância de armazenamento do Azure][storage-skus] para *skuName*:

* *Standard_LRS* -LRS (armazenamento com redundância local) padrão
* *Standard_GRS* -armazenamento com redundância geográfica padrão (GRS)
* *Standard_RAGRS* -armazenamento com redundância geográfica com acesso de leitura padrão (ra-grs)
* *Premium_LRS* -LRS (armazenamento com redundância local) Premium

> [!NOTE]
> Os arquivos do Azure dão suporte ao armazenamento Premium em clusters AKS que executam o kubernetes 1,13 ou superior.

Para obter mais informações sobre classes de armazenamento kubernetes para arquivos do Azure, consulte [classes de armazenamento kubernetes][kubernetes-storage-classes].

Crie um arquivo chamado `azure-file-sc.yaml` e copie no exemplo de manifesto a seguir. Para obter mais informações sobre o *mountoptions*, consulte a seção [Opções de montagem][mount-options] .

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
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Crie a classe de armazenamento com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Criar uma função de cluster e uma associação

Os clusters AKS usam o RBAC (controle de acesso baseado em função) do kubernetes para limitar as ações que podem ser executadas. As *funções* definem as permissões a serem concedidas e as *associações* as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [usando a autorização do RBAC][kubernetes-rbac].

Para permitir que a plataforma Azure crie os recursos de armazenamento necessários, crie um *ClusterRole* e um *ClusterRoleBinding*. Crie um arquivo chamado `azure-pvc-roles.yaml` e copie o seguinte YAML:

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

Atribua as permissões com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Criar uma declaração de volume persistente

Uma declaração de volume persistente (PVC) usa o objeto de classe de armazenamento para provisionar dinamicamente um compartilhamento de arquivos do Azure. O YAML a seguir pode ser usado para criar uma declaração de volume persistente de *5 GB* de tamanho com acesso *ReadWriteMany* . Para obter mais informações sobre modos de acesso, consulte a documentação de [volume persistente do kubernetes][access-modes] .

Agora, crie um arquivo `azure-file-pvc.yaml` chamado e copie o YAML a seguir. Certifique-se de que *storageClassName* corresponde à classe de armazenamento criada na última etapa:

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

> [!NOTE]
> Se estiver usando o SKU *Premium_LRS* para sua classe de armazenamento, o valor mínimo para *armazenamento* deve ser *100gi*.

Crie a declaração de volume persistente com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

Depois de concluído, o compartilhamento de arquivos será criado. Um segredo kubernetes também é criado e inclui informações de conexão e credenciais. Você pode usar o comando [Get do kubectl][kubectl-get] para exibir o status do PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Usar o volume persistente

O YAML a seguir cria um pod que usa o *azurefile* de declaração de volume persistente para montar o compartilhamento de arquivos do Azure no caminho */mnt/Azure* . Para contêineres do Windows Server (atualmente em visualização em AKS), especifique um *mountPath* usando a Convenção de caminho do Windows, como *: '* .

Crie um arquivo chamado `azure-pvc-files.yaml`e copie o YAML a seguir. Verifique se *claimname* corresponde ao PVC criado na última etapa.

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

Crie o Pod com o comando [kubectl Apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

Agora você tem um pod em execução com o compartilhamento de arquivos do Azure montado no diretório */mnt/Azure* Essa configuração pode ser vista ao inspecionar o Pod via `kubectl describe pod mypod`. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

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

O valor padrão de *fileMode* e *dirMode* é *0755* para kubernetes versão 1.9.1 e superior. Se estiver usando um cluster com Kuberetes versão 1.8.5 ou superior e criando dinamicamente o volume persistente com uma classe de armazenamento, as opções de montagem poderão ser especificadas no objeto de classe de armazenamento. O exemplo a seguir define *0777*:

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
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Se estiver usando um cluster da versão 1.8.0-1.8.4, um contexto de segurança poderá ser especificado com o valor de *runAsUser* definido como *0*. Para obter mais informações sobre o contexto de segurança Pod, consulte [configurar um contexto de segurança][kubernetes-security-context].

## <a name="next-steps"></a>Passos Seguintes

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para armazenamento e backups em AKs][operator-best-practices-storage].

Saiba mais sobre volumes persistentes kubernetes usando os arquivos do Azure.

> [!div class="nextstepaction"]
> [Plug-in kubernetes para arquivos do Azure][kubernetes-files]

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