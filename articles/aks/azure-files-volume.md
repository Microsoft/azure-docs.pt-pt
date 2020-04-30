---
title: Criar manualmente a partilha de Ficheiros Azure
titleSuffix: Azure Kubernetes Service
description: Aprenda a criar manualmente um volume com Ficheiros Azure para utilização com várias cápsulas simultâneas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 144d93cbb3b66f260dbd9d92863ca5fb13ed00a5
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207671"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Criar e utilizar manualmente um volume com a participação de Ficheiros Azure no Serviço Azure Kubernetes (AKS)

As aplicações baseadas em contentores precisam frequentemente de aceder e persistir dados num volume externo de dados. Se várias cápsulas necessitarem de acesso simultâneo ao mesmo volume de armazenamento, pode utilizar ficheiros Azure para se ligar utilizando o protocolo do Bloco de [Mensagens do Servidor (SMB).][smb-overview] Este artigo mostra-lhe como criar manualmente uma partilha de Ficheiros Azure e anexá-la a uma cápsula em AKS.

Para obter mais informações sobre os volumes kubernetes, consulte as opções de [armazenamento para aplicações em AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Antes de poder utilizar o Azure Files como volume Kubernetes, tem de criar uma conta de Armazenamento Azure e a partilha de ficheiros. Os seguintes comandos criam um grupo de recursos chamado *myAKSShare,* uma conta de armazenamento, e uma partilha de Ficheiros com o nome *aksshare*:

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
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Tome nota do nome da conta de armazenamento e da chave mostrada no final da saída do script. Estes valores são necessários quando se cria o volume Kubernetes num dos seguintes passos.

## <a name="create-a-kubernetes-secret"></a>Criar um segredo kubernetes

Kubernetes precisa de credenciais para aceder à parte de ficheiro criada no passo anterior. Estas credenciais são armazenadas num [segredo kubernetes,][kubernetes-secret]que é referenciado quando se cria uma cápsula Kubernetes.

Use `kubectl create secret` o comando para criar o segredo. O exemplo seguinte cria um nome compartilhado chamado *azure-secret* e povoa o nome de *conta de armazenamento azul* e o key de contabilidade *azurestorage* do passo anterior. Para utilizar uma conta de armazenamento Azure existente, forneça o nome da conta e a chave.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Monte a parte do ficheiro como um volume

Para montar os Ficheiros Azure, configure o volume na especificação `azure-files-pod.yaml` do recipiente. Se alterar o nome da partilha de Ficheiros ou nome secreto, atualize o *nome de partilha* e o nome *secreto*. Se desejar, atualize o `mountPath`, que é o caminho onde a partilha de Ficheiros é montada na cápsula. Para os recipientes do Windows Server, especifique um *mountPath* utilizando a convenção do caminho do Windows, como *'D:'*.

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

Use `kubectl` o comando para criar a cápsula.

```console
kubectl apply -f azure-files-pod.yaml
```

Tem agora uma cápsula de corrida com uma partilha de Ficheiros Azure montada em */mnt/azure*. Pode utilizar `kubectl describe pod mypod` para verificar se a parte está montada com sucesso. A saída de exemplo condensado seguinte mostra o volume montado no recipiente:

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

O valor padrão para *fileMode* e *dirMode* é *0755* para kubernetes versão 1.9.1 e superior. Se utilizar um cluster com a versão 1.8.5 ou maior e estática criando o objeto de volume persistente, as opções de montagem devem ser especificadas no objeto *PersistentVolume.* O exemplo que se segue é o *0777:*

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
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Se utilizar um cluster da versão 1.8.0 - 1.8.4, um contexto de segurança pode ser especificado com o valor *runAsUser* definido para *0*. Para obter mais informações sobre o contexto de segurança do Pod, consulte [Configure um Contexto de Segurança][kubernetes-security-context].

Para atualizar as suas opções de montagem, crie um ficheiro *azurefile-mount-options-pv.yaml* com um *PersistentVolume*. Por exemplo:

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
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Crie um ficheiro *azurefile-mount-options-pvc.yaml* com um *PersistentVolumeClaim* que utiliza o *PersistenteVolume*. Por exemplo:

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

Utilize `kubectl` os comandos para criar o *Volume Persistente* e *persistenteVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Verifique se a sua *PersistenteVolumeClaim* é criada e ligada ao *Volume Persistente*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Atualize a especificação do seu recipiente para fazer referência ao seu *PersistentVolumeClaim* e atualize o seu casulo. Por exemplo:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Passos seguintes

Para as melhores práticas associadas, consulte [as melhores práticas de armazenamento e backups em AKS][operator-best-practices-storage].

Para obter mais informações sobre os clusters AKS interaja com ficheiros Azure, consulte o [plugin Kubernetes para Ficheiros Azure][kubernetes-files].

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
