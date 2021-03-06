---
title: Criar manualmente a partilha de Ficheiros Azure
titleSuffix: Azure Kubernetes Service
description: Saiba como criar manualmente um volume com ficheiros Azure para utilização com várias cápsulas simultâneas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7f3c8ae63e908f440740277084293a011b80b9d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776093"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Criar e utilizar manualmente um volume com a partilha de Ficheiros Azure no Serviço Azure Kubernetes (AKS)

As aplicações baseadas em contentores precisam frequentemente de aceder e persistir dados num volume de dados externo. Se várias cápsulas precisarem de acesso simultâneo ao mesmo volume de armazenamento, pode utilizar ficheiros Azure para se ligar utilizando o protocolo Bloco de Mensagens do [Servidor (SMB).][smb-overview] Este artigo mostra-lhe como criar manualmente uma partilha de Ficheiros Azure e anexá-lo a uma cápsula em AKS.

Para obter mais informações sobre volumes kubernetes, consulte [as opções de Armazenamento para aplicações em AKS][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Antes de poder utilizar os Ficheiros Azure como volume de Kubernetes, tem de criar uma conta de Armazenamento Azure e a partilha de ficheiros. Os seguintes comandos criam um grupo de recursos chamado *myAKSShare*, uma conta de armazenamento, e uma partilha de Ficheiros chamada *aksshare*:

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

Tome nota do nome da conta de armazenamento e da chave mostrada no final da saída do script. Estes valores são necessários quando cria o volume Kubernetes num dos seguintes passos.

## <a name="create-a-kubernetes-secret"></a>Criar um segredo de Kubernetes

Kubernetes precisa de credenciais para aceder à partilha de ficheiros criada no passo anterior. Estas credenciais são [armazenadas num segredo de Kubernetes,][kubernetes-secret]que é referenciado quando se cria uma cápsula Kubernetes.

Use o `kubectl create secret` comando para criar o segredo. O exemplo a seguir cria um *nome azure-secret* e povoa o nome de *contatímero de azurestorage* e *azurestorageaccountkey* do passo anterior. Para utilizar uma conta de armazenamento Azure existente, forneça o nome e a chave da conta.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Monte a partilha de ficheiros como um volume inline
> Nota: a partir de 1.18.15, 1.19.7, 1.20.2, 1.21.0, o espaço de nomes secretos no volume inline `azureFile` só pode ser definido como espaço de `default` nome, para especificar um espaço de nome secreto diferente, por favor use abaixo o exemplo de volume persistente.

Para montar os Ficheiros Azure partilhem na sua cápsula, configuure o volume na especificação do recipiente. Crie um novo ficheiro com `azure-files-pod.yaml` o seguinte conteúdo. Se alterar o nome da partilha de Ficheiros ou nome secreto, atualize o *nome de partilha* e o nome *secreto*. Se desejar, atualize o `mountPath` , que é o caminho onde a partilha de Ficheiros é montada na cápsula. Para os recipientes do Windows Server, especifique um *mountPath* utilizando a convenção do caminho do Windows, como *'D:'*.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Use o `kubectl` comando para criar a cápsula.

```console
kubectl apply -f azure-files-pod.yaml
```

Tem agora uma cápsula de corrida com uma partilha de Ficheiros Azure montada em */mnt/azure*. Pode utilizar `kubectl describe pod mypod` para verificar se a parte está montada com sucesso. A seguinte saída de exemplo condensada mostra o volume montado no recipiente:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

## <a name="mount-file-share-as-an-persistent-volume"></a>Monte a partilha de ficheiros como um volume persistente
 - Opções de montagem

O valor predefinido para *ficheirosMode* e *dirMode* é *0777* para a versão 1.15 de Kubernetes ou superior. O exemplo a seguir define *0755* no objeto *PersistenteVolume:*

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
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

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

Crie um ficheiro *azurefile-mount-options-pvc.yaml* com um *PersistenteVolumeClaim* que utiliza o *PersistenteVolume*. Por exemplo:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

Utilize os `kubectl` comandos para criar o *PersistentVolume* e *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Verifique se o seu *PersistenteVolumeClaim* foi criado e ligado ao *PersistenteVolume*.

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

Para obter mais informações sobre os clusters AKS interagem com os Ficheiros Azure, consulte o [plugin Kubernetes para Ficheiros Azure][kubernetes-files].

Para parâmetros de classe de armazenamento, consulte [Disposição Estática (traga a sua própria parte de ficheiro)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
