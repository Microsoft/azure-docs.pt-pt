---
title: Crie uma cópia de segurança da aplicação de cluster Azure Red Hat OpenShift 4 utilizando Velero
description: Saiba como criar uma cópia de segurança das suas aplicações de cluster Azure Red Hat OpenShift usando Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.custom: mvc
ms.openlocfilehash: bbfe280ed0b1b562e0f50b23a09ea159750c4a79
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217096"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Crie um Azure Red Hat OpenShift 4 Cluster Application Backup

Neste artigo, você vai preparar o seu ambiente para criar um backup de aplicação de cluster Azure Red Hat OpenShift 4. Vai aprender a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e instalar as ferramentas necessárias
> * Crie um Azure Red Hat OpenShift 4 backup de aplicações

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

### <a name="install-velero"></a>Instalar Velero

Para [instalar](https://velero.io/docs/main/basic-install/) o Velero no seu sistema, siga o processo recomendado para o seu sistema operativo.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Configurar a conta de armazenamento Azure e o recipiente Blob

Este passo criará um grupo de recursos fora do grupo de recursos do cluster ARO.  Este grupo de recursos permitirá que as cópias de segurança persistam e podem restaurar as aplicações em novos clusters.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Definir permissões para Velero

### <a name="create-service-principal"></a>Criar um principal de serviço

Velero precisa de permissões para fazer backups e restauros. Quando cria um principal de serviço, está a dar permissão ao Velero para aceder ao grupo de recursos que define no passo anterior. Este passo irá obter o grupo de recursos do cluster:

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Instale Velero no aglomerado Azure Red Hat OpenShift 4

Este passo irá instalar o Velero no seu próprio projeto e nas [definições de recursos personalizados necessárias](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) para fazer backups e restauros com o Velero. Certifique-se de que está ligado com sucesso a um cluster V4 do Chapéu Vermelho Azure.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Criar uma cópia de segurança com Velero

Para criar uma cópia de segurança da aplicação com o Velero, terá de incluir o espaço de nome em que esta aplicação se encontra.  Se tiver um `nginx-example` espaço de nome e quiser incluir todos os recursos nesse espaço de nome na cópia de segurança, execute o seguinte comando no terminal:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
Pode verificar o estado da cópia de segurança executando:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Uma cópia de segurança bem sucedida irá funcionar `phase:Completed` e os objetos viverão no recipiente na conta de armazenamento.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Crie uma cópia de segurança com Velero para incluir instantâneos

Para criar uma cópia de segurança da aplicação com o Velero para incluir os volumes persistentes da sua aplicação, terá de incluir o espaço de nome em que a aplicação se encontra, bem como incluir a bandeira ao `snapshot-volumes=true` criar o backup

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

Pode verificar o estado da cópia de segurança executando:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Uma cópia de segurança bem sucedida com saída `phase:Completed` e os objetos viverão no recipiente na conta de armazenamento.

Para mais informações sobre como criar backups e restauros usando Velero consulte [backup Recursos OpenShift da forma nativa](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, foi apoiada uma aplicação de cluster Azure Red Hat OpenShift 4. Aprendeu a:

> [!div class="checklist"]
> * Crie uma cópia de segurança da aplicação OpenShift v4 utilizando o Velero
> * Crie uma cópia de segurança da aplicação openShift v4 com instantâneos usando Velero


Avance para o próximo artigo para aprender como criar uma aplicação de cluster Azure Red Hat OpenShift 4 restaurada.

* [Crie um Azure Red Hat OpenShift 4 cluster app restaurar](howto-create-a-restore.md)
* [Crie um Azure Red Hat OpenShift 4 cluster app restaurar, incluindo instantâneos](howto-create-a-restore.md)
