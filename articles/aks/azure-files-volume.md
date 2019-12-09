---
title: Criar um volume estático para vários pods no serviço de kubernetes do Azure (AKS)
description: Saiba como criar um volume manualmente com arquivos do Azure para uso com vários pods simultâneos no AKS (serviço kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 7b5f7c25cd1627475d8e37a539956f01ae6151ab
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914026"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Criar e usar manualmente um volume com o compartilhamento de arquivos do Azure no serviço de kubernetes do Azure (AKS)

Aplicativos baseados em contêiner geralmente precisam acessar e manter dados em um volume de dados externo. Se vários pods precisarem de acesso simultâneo ao mesmo volume de armazenamento, você poderá usar os arquivos do Azure para se conectar usando o [protocolo SMB][smb-overview]. Este artigo mostra como criar manualmente um compartilhamento de arquivos do Azure e anexá-lo a um pod no AKS.

Para obter mais informações sobre volumes kubernetes, consulte [Opções de armazenamento para aplicativos em AKs][concepts-storage].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Antes de poder usar os arquivos do Azure como um volume kubernetes, você deve criar uma conta de armazenamento do Azure e o compartilhamento de arquivos. Os comandos a seguir criam um grupo de recursos chamado *myAKSShare*, uma conta de armazenamento e um compartilhamento de arquivos chamado *aksshare*:

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

Anote o nome da conta de armazenamento e a chave mostrados no final da saída do script. Esses valores são necessários quando você cria o volume kubernetes em uma das etapas a seguir.

## <a name="create-a-kubernetes-secret"></a>Criar um segredo kubernetes

O kubernetes precisa de credenciais para acessar o compartilhamento de arquivos criado na etapa anterior. Essas credenciais são armazenadas em um [segredo kubernetes][kubernetes-secret], que é referenciado quando você cria um pod kubernetes.

Use o comando `kubectl create secret` para criar o segredo. O exemplo a seguir cria um compartilhado chamado *Azure-Secret* e popula o *azurestorageaccountname* e o *azurestorageaccountkey* da etapa anterior. Para usar uma conta de armazenamento do Azure existente, forneça o nome da conta e a chave.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Montar o compartilhamento de arquivos como um volume

Para montar o compartilhamento de arquivos do Azure em seu Pod, configure o volume na especificação do contêiner. Crie um novo arquivo chamado `azure-files-pod.yaml` com o conteúdo a seguir. Se você alterou o nome do compartilhamento de arquivos ou nome do segredo, atualize o *ShareName* e o *secretname*. Se desejar, atualize o `mountPath`, que é o caminho onde o compartilhamento de arquivos é montado no pod. Para contêineres do Windows Server (atualmente em visualização em AKS), especifique um *mountPath* usando a Convenção de caminho do Windows, como *: '* .

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

Use o comando `kubectl` para criar o pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Agora você tem um pod em execução com um compartilhamento de arquivos do Azure montado em */mnt/Azure*. Você pode usar `kubectl describe pod mypod` para verificar se o compartilhamento foi montado com êxito. A seguinte saída de exemplo condensada mostra o volume montado no contêiner:

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

O valor padrão de *fileMode* e *dirMode* é *0755* para kubernetes versão 1.9.1 e superior. Se estiver usando um cluster com Kuberetes versão 1.8.5 ou superior e criando estaticamente o objeto de volume persistente, as opções de montagem precisarão ser especificadas no objeto *PersistentVolume* . O exemplo a seguir define *0777*:

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

Se estiver usando um cluster da versão 1.8.0-1.8.4, um contexto de segurança poderá ser especificado com o valor de *runAsUser* definido como *0*. Para obter mais informações sobre o contexto de segurança Pod, consulte [configurar um contexto de segurança][kubernetes-security-context].

Para atualizar suas opções de montagem, crie um arquivo *azurefile-Mount-Options-PV. YAML* com um *PersistentVolume*. Por exemplo:

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

Crie um arquivo *azurefile-Mount-Options-PVC. YAML* com um *PersistentVolumeClaim* que usa o *PersistentVolume*. Por exemplo:

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

Use os comandos `kubectl` para criar o *PersistentVolume* e o *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Verifique se o *PersistentVolumeClaim* foi criado e associado ao *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Atualize a especificação do contêiner para fazer referência ao seu *PersistentVolumeClaim* e atualize seu Pod. Por exemplo:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Passos seguintes

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para armazenamento e backups em AKs][operator-best-practices-storage].

Para obter mais informações sobre os clusters AKS interagem com os arquivos do Azure, consulte o [plug-in kubernetes para arquivos do Azure][kubernetes-files].

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
