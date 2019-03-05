---
title: Criar um volume estático para vários pods no Azure Kubernetes Service (AKS)
description: Saiba como criar manualmente um volume com ficheiros do Azure para utilização com vários pods em simultâneo no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: f8558529df24c0aaede0c58744e17829ec0b5669
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337537"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Crie manualmente e usar um volume com a partilha de ficheiros do Azure no Azure Kubernetes Service (AKS)

Aplicações baseadas em contentores, muitas vezes, tem de aceder e manter os dados num volume de dados externa. Se precisam de vários pods acesso simultâneo no mesmo volume de armazenamento, pode utilizar ficheiros do Azure para ligar através da [protocolo Server Message Block (SMB)][smb-overview]. Este artigo mostra-lhe como criar uma partilha de ficheiros do Azure e anexá-lo a um pod no AKS manualmente.

Para obter mais informações sobre volumes do Kubernetes, consulte [opções de armazenamento para aplicações no AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Antes de poder utilizar os ficheiros do Azure como um volume do Kubernetes, tem de criar uma conta de armazenamento do Azure e a partilha de ficheiros. Os comandos seguintes criam um grupo de recursos chamado *myAKSShare*, uma conta de armazenamento e uma partilha de ficheiros com o nome *aksshare*:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Anote o nome da conta de armazenamento e a chave mostrado no final de saída do script. Estes valores são necessários quando criar o volume do Kubernetes em um dos seguintes passos.

## <a name="create-a-kubernetes-secret"></a>Crie um Kubernetes secreta

Kubernetes necessita de credenciais para aceder à partilha de ficheiro criada no passo anterior. Estas credenciais são armazenadas num [segredo do Kubernetes][kubernetes-secret], que é referenciado quando cria um pod de Kubernetes.

Utilize o `kubectl create secret` comando para criar o segredo. O exemplo seguinte cria um partilhado com o nome *azure-secret* e preenche o *azurestorageaccountname* e *azurestorageaccountkey* do passo anterior. Para utilizar uma conta de armazenamento do Azure existente, forneça o nome da conta e a chave.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Montar a partilha de ficheiros como um volume

Para montar a partilha de ficheiros do Azure no seu pod, configure o volume na especificação do contentor. Crie um novo ficheiro designado `azure-files-pod.yaml` com o seguinte conteúdo. Se tiver alterado o nome da partilha de ficheiros ou nome do segredo, atualize o *shareName* e *secretName*. Se assim o desejar, atualize o `mountPath`, que é o caminho onde os ficheiros partilham esteja montada no pod.

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
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Utilize o `kubectl` comando para criar o pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Tem agora um pod em execução com uma partilha de ficheiros do Azure montada em */mnt/azure*. Pode usar `kubectl describe pod mypod` para verificar a partilha é montada com êxito. O resultado de exemplo condensado seguinte mostra o volume montado no contentor:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
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

Se utilizar um cluster de versão 1.8.5 ou superior e estaticamente criar o objeto de volume persistente, opções de montagem precisam ser especificado o *PersistentVolume* objeto.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
```

Se utilizar um cluster de versão 1.8.0 - 1.8.4, um contexto de segurança pode ser especificado com o *runAsUser* valor definido como *0*. Para obter mais informações sobre o contexto de segurança de Pod, consulte [configurar um contexto de segurança][kubernetes-security-context].

## <a name="next-steps"></a>Passos Seguintes

Para as práticas recomendadas associadas, consulte [melhores práticas para o armazenamento e cópias de segurança no AKS][operator-best-practices-storage].

Para obter mais informações sobre clusters do AKS interagir com os ficheiros do Azure, consulte a [Plug-in do Kubernetes para ficheiros do Azure][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
